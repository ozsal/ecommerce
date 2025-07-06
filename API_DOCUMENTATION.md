# E-Commerce Laravel Application - API Documentation

## Table of Contents
1. [Overview](#overview)
2. [Models](#models)
3. [Controllers](#controllers)
4. [API Routes](#api-routes)
5. [Web Routes](#web-routes)
6. [Helper Functions](#helper-functions)
7. [Frontend Components](#frontend-components)
8. [Configuration](#configuration)
9. [Setup Instructions](#setup-instructions)
10. [Examples and Usage](#examples-and-usage)

---

## Overview

This is a comprehensive Laravel-based e-commerce application featuring:
- Product catalog management
- Shopping cart and wishlist functionality
- User authentication and roles (Admin/User)
- Order management system
- Blog functionality
- Payment integration (PayPal)
- Category and brand management
- Product reviews and ratings

### Tech Stack
- **Backend**: Laravel PHP Framework
- **Frontend**: Blade Templates, Bootstrap 4, jQuery, Vue.js
- **Database**: MySQL (configurable)
- **Assets**: Laravel Mix, Sass
- **Payment**: PayPal integration
- **File Management**: Laravel File Manager

---

## Models

### User Model
**Location**: `app/User.php`

#### Properties
```php
protected $fillable = [
    'name', 'email', 'password', 'role', 'photo', 'status', 'provider', 'provider_id'
];
```

#### Relationships
```php
// Get user orders
public function orders()
{
    return $this->hasMany('App\Models\Order');
}
```

#### Usage Example
```php
// Get authenticated user's orders
$user = Auth::user();
$orders = $user->orders()->where('status', 'delivered')->get();
```

---

### Product Model
**Location**: `app/Models/Product.php`

#### Properties
```php
protected $fillable = [
    'title', 'slug', 'summary', 'description', 'cat_id', 'child_cat_id', 
    'price', 'brand_id', 'discount', 'status', 'photo', 'size', 'stock', 
    'is_featured', 'condition'
];
```

#### Key Methods

##### `getAllProduct()`
Returns paginated products with category information.
```php
Product::getAllProduct(); // Returns 10 products per page with categories
```

##### `getProductBySlug($slug)`
Get product details by slug with reviews and related products.
```php
$product = Product::getProductBySlug('laptop-dell-inspiron');
// Returns product with categories, reviews, and related products
```

##### `countActiveProduct()`
Count all active products.
```php
$count = Product::countActiveProduct();
```

#### Relationships
```php
// Product category
public function cat_info()
{
    return $this->hasOne('App\Models\Category', 'id', 'cat_id');
}

// Product subcategory
public function sub_cat_info()
{
    return $this->hasOne('App\Models\Category', 'id', 'child_cat_id');
}

// Related products
public function rel_prods()
{
    return $this->hasMany('App\Models\Product', 'cat_id', 'cat_id')
           ->where('status', 'active')
           ->orderBy('id', 'DESC')
           ->limit(8);
}

// Product reviews
public function getReview()
{
    return $this->hasMany('App\Models\ProductReview', 'product_id', 'id')
           ->with('user_info')
           ->where('status', 'active')
           ->orderBy('id', 'DESC');
}
```

---

### Category Model
**Location**: `app/Models/Category.php`

#### Properties
```php
protected $fillable = [
    'title', 'slug', 'summary', 'photo', 'status', 'is_parent', 'parent_id', 'added_by'
];
```

#### Key Methods

##### `getAllParentWithChild()`
Get all parent categories with their child categories.
```php
$categories = Category::getAllParentWithChild();
// Returns categories with nested child categories
```

##### `getProductByCat($slug)`
Get all products in a category.
```php
$categoryProducts = Category::getProductByCat('electronics');
```

##### `getChildByParentID($id)`
Get child categories by parent ID.
```php
$subcategories = Category::getChildByParentID(1);
```

#### Relationships
```php
// Parent category
public function parent_info()
{
    return $this->hasOne('App\Models\Category', 'id', 'parent_id');
}

// Child categories
public function child_cat()
{
    return $this->hasMany('App\Models\Category', 'parent_id', 'id')
           ->where('status', 'active');
}

// Category products
public function products()
{
    return $this->hasMany('App\Models\Product', 'cat_id', 'id')
           ->where('status', 'active');
}
```

---

### Order Model
**Location**: `app/Models/Order.php`

#### Properties
```php
protected $fillable = [
    'user_id', 'order_number', 'sub_total', 'quantity', 'delivery_charge', 
    'status', 'total_amount', 'first_name', 'last_name', 'country', 
    'post_code', 'address1', 'address2', 'phone', 'email', 'payment_method', 
    'payment_status', 'shipping_id', 'coupon'
];
```

#### Key Methods

##### `getAllOrder($id)`
Get order with cart information.
```php
$order = Order::getAllOrder(123);
// Returns order with all cart items
```

##### `countActiveOrder()`
Count total orders.
```php
$totalOrders = Order::countActiveOrder();
```

#### Relationships
```php
// Order cart items
public function cart_info()
{
    return $this->hasMany('App\Models\Cart', 'order_id', 'id');
}

// Order shipping
public function shipping()
{
    return $this->belongsTo(Shipping::class, 'shipping_id');
}

// Order user
public function user()
{
    return $this->belongsTo('App\User', 'user_id');
}
```

---

### Cart Model
**Location**: `app/Models/Cart.php`

#### Properties
```php
protected $fillable = [
    'user_id', 'product_id', 'order_id', 'quantity', 'amount', 'price', 'status'
];
```

#### Relationships
```php
// Cart product
public function product()
{
    return $this->hasOne('App\Models\Product', 'id', 'product_id');
}
```

---

## Controllers

### FrontendController
**Location**: `app/Http/Controllers/FrontendController.php`

Main controller handling public-facing functionality.

#### Key Methods

##### `home()`
**Route**: `GET /`
**Purpose**: Display homepage with featured products, posts, and banners.

```php
// Usage: Automatically called when visiting the homepage
// Returns: Homepage view with featured products, recent posts, banners, and categories
```

##### `productDetail($slug)`
**Route**: `GET /product-detail/{slug}`
**Purpose**: Display single product details.

```php
// Usage: Visit /product-detail/laptop-dell-inspiron
// Returns: Product detail page with reviews and related products
```

##### `productGrids()`
**Route**: `GET /product-grids`
**Purpose**: Display products in grid layout with filtering.

**Query Parameters:**
- `category`: Filter by category slugs (comma-separated)
- `brand`: Filter by brand slugs (comma-separated)
- `sortBy`: Sort by 'title' or 'price'
- `price`: Price range filter (e.g., "100-500")
- `show`: Number of products per page

```php
// Usage Examples:
// /product-grids?category=electronics,clothing
// /product-grids?brand=apple,samsung&sortBy=price
// /product-grids?price=100-1000&show=12
```

##### `productSearch(Request $request)`
**Route**: `POST /product/search`
**Purpose**: Search products by keyword.

```php
// POST data: {'search': 'laptop'}
// Returns: Products matching the search term
```

##### `login()` & `loginSubmit(Request $request)`
**Routes**: 
- `GET /user/login`
- `POST /user/login`

**Purpose**: Handle user authentication.

```php
// POST data for login:
{
    "email": "user@example.com",
    "password": "password123"
}
```

##### `register()` & `registerSubmit(Request $request)`
**Routes**:
- `GET /user/register`
- `POST /user/register`

**Purpose**: Handle user registration.

```php
// POST data for registration:
{
    "name": "John Doe",
    "email": "john@example.com", 
    "password": "password123",
    "password_confirmation": "password123"
}
```

---

### CartController
**Location**: `app/Http/Controllers/CartController.php`

Handles shopping cart functionality.

#### Key Methods

##### `addToCart(Request $request)`
**Route**: `GET /add-to-cart/{slug}`
**Purpose**: Add product to cart (single quantity).

```php
// Usage: Visit /add-to-cart/laptop-dell-inspiron
// Requires: User authentication
// Action: Adds 1 quantity of product to cart
```

##### `singleAddToCart(Request $request)`
**Route**: `POST /add-to-cart`
**Purpose**: Add product to cart with specific quantity.

```php
// POST data:
{
    "slug": "laptop-dell-inspiron",
    "quant": [1, 3]  // [index, quantity]
}
```

##### `cartUpdate(Request $request)`
**Route**: `POST /cart-update`
**Purpose**: Update cart item quantities.

```php
// POST data:
{
    "quant": [2, 1, 3],      // New quantities
    "qty_id": [1, 2, 3]      // Cart item IDs
}
```

##### `cartDelete(Request $request)`
**Route**: `GET /cart-delete/{id}`
**Purpose**: Remove item from cart.

```php
// Usage: Visit /cart-delete/5
// Action: Removes cart item with ID 5
```

##### `checkout()`
**Route**: `GET /checkout`
**Purpose**: Display checkout page.

```php
// Requires: User authentication and items in cart
// Returns: Checkout page with cart summary
```

---

### AdminController
**Location**: `app/Http/Controllers/AdminController.php`

Handles admin panel functionality.

#### Key Methods

##### `index()`
**Route**: `GET /admin`
**Purpose**: Admin dashboard.

##### `profile()`
**Route**: `GET /admin/profile`
**Purpose**: Admin profile management.

##### `settings()`
**Route**: `GET /admin/settings`
**Purpose**: Application settings management.

---

## API Routes

**File**: `routes/api.php`

### Authentication Endpoint

#### Get Authenticated User
**Endpoint**: `GET /api/user`
**Middleware**: `auth:api`
**Purpose**: Get authenticated user information.

```php
// Headers:
Authorization: Bearer {api_token}

// Response:
{
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com",
    "role": "user",
    "status": "active"
}
```

---

## Web Routes

**File**: `routes/web.php`

### Frontend Routes

#### Homepage
- `GET /` → `FrontendController@home`

#### Product Routes
- `GET /product-detail/{slug}` → `FrontendController@productDetail`
- `POST /product/search` → `FrontendController@productSearch`
- `GET /product-cat/{slug}` → `FrontendController@productCat`
- `GET /product-sub-cat/{slug}/{sub_slug}` → `FrontendController@productSubCat`
- `GET /product-brand/{slug}` → `FrontendController@productBrand`
- `GET /product-grids` → `FrontendController@productGrids`
- `GET /product-lists` → `FrontendController@productLists`
- `MATCH /filter` → `FrontendController@productFilter`

#### Cart Routes (Requires Authentication)
- `GET /add-to-cart/{slug}` → `CartController@addToCart`
- `POST /add-to-cart` → `CartController@singleAddToCart`
- `GET /cart-delete/{id}` → `CartController@cartDelete`
- `POST /cart-update` → `CartController@cartUpdate`
- `GET /cart` → Cart page view
- `GET /checkout` → `CartController@checkout`

#### Wishlist Routes (Requires Authentication)
- `GET /wishlist` → Wishlist page view
- `GET /wishlist/{slug}` → `WishlistController@wishlist`
- `GET /wishlist-delete/{id}` → `WishlistController@wishlistDelete`

#### Order Routes
- `POST /cart/order` → `OrderController@store`
- `GET /order/pdf/{id}` → `OrderController@pdf`
- `GET /product/track` → `OrderController@orderTrack`
- `POST /product/track/order` → `OrderController@productTrackOrder`

#### Blog Routes
- `GET /blog` → `FrontendController@blog`
- `GET /blog-detail/{slug}` → `FrontendController@blogDetail`
- `GET /blog/search` → `FrontendController@blogSearch`
- `POST /blog/filter` → `FrontendController@blogFilter`
- `GET /blog-cat/{slug}` → `FrontendController@blogByCategory`
- `GET /blog-tag/{slug}` → `FrontendController@blogByTag`

### Admin Routes (Requires Admin Role)
**Prefix**: `/admin`
**Middleware**: `['auth', 'admin']`

#### Dashboard
- `GET /admin` → `AdminController@index`

#### Resource Management
- `RESOURCE /admin/users` → `UsersController`
- `RESOURCE /admin/banner` → `BannerController`
- `RESOURCE /admin/brand` → `BrandController`
- `RESOURCE /admin/category` → `CategoryController`
- `RESOURCE /admin/product` → `ProductController`
- `RESOURCE /admin/post` → `PostController`
- `RESOURCE /admin/order` → `OrderController`
- `RESOURCE /admin/coupon` → `CouponController`

### User Routes (Requires User Authentication)
**Prefix**: `/user`
**Middleware**: `['user']`

#### Dashboard
- `GET /user` → `HomeController@index`

#### Profile Management
- `GET /user/profile` → `HomeController@profile`
- `POST /user/profile/{id}` → `HomeController@profileUpdate`

#### Order Management
- `GET /user/order` → `HomeController@orderIndex`
- `GET /user/order/show/{id}` → `HomeController@orderShow`
- `DELETE /user/order/delete/{id}` → `HomeController@userOrderDelete`

---

## Helper Functions

**File**: `app/Http/Helpers.php`

### Cart Helper Functions

#### `Helper::cartCount($user_id = '')`
Get cart item count for user.

```php
$cartCount = Helper::cartCount(); // For authenticated user
$cartCount = Helper::cartCount(123); // For specific user
```

#### `Helper::getAllProductFromCart($user_id = '')`
Get all cart products for user.

```php
$cartItems = Helper::getAllProductFromCart();
// Returns collection of cart items with product details
```

#### `Helper::totalCartPrice($user_id = '')`
Calculate total cart price.

```php
$total = Helper::totalCartPrice();
// Returns total amount in cart
```

### Wishlist Helper Functions

#### `Helper::wishlistCount($user_id = '')`
Get wishlist item count.

```php
$wishlistCount = Helper::wishlistCount();
```

#### `Helper::getAllProductFromWishlist($user_id = '')`
Get all wishlist products.

```php
$wishlistItems = Helper::getAllProductFromWishlist();
```

### Category Helper Functions

#### `Helper::getAllCategory()`
Get all categories with parent-child relationship.

```php
$categories = Helper::getAllCategory();
```

#### `Helper::getHeaderCategory()`
Generate HTML for header category menu.

```php
Helper::getHeaderCategory(); // Outputs HTML menu structure
```

#### `Helper::productCategoryList($option = 'all')`
Get category list for products.

```php
$allCategories = Helper::productCategoryList('all');
$categoriesWithProducts = Helper::productCategoryList('with_products');
```

### Statistics Helper Functions

#### `Helper::earningPerMonth()`
Calculate monthly earnings from delivered orders.

```php
$monthlyEarnings = Helper::earningPerMonth();
```

#### `Helper::shipping()`
Get all shipping methods.

```php
$shippingMethods = Helper::shipping();
```

---

## Frontend Components

### Views Structure
**Location**: `resources/views/`

#### Frontend Views
- `frontend/index.blade.php` - Homepage
- `frontend/pages/product_detail.blade.php` - Product detail page
- `frontend/pages/product-grids.blade.php` - Product grid layout
- `frontend/pages/product-lists.blade.php` - Product list layout
- `frontend/pages/cart.blade.php` - Shopping cart
- `frontend/pages/checkout.blade.php` - Checkout page
- `frontend/pages/wishlist.blade.php` - Wishlist page
- `frontend/pages/blog.blade.php` - Blog listing
- `frontend/pages/blog-detail.blade.php` - Blog post detail

#### Backend Views
- `backend/` - Admin panel views
- `backend/layouts/` - Admin layout templates

#### User Dashboard Views
- `user/` - User dashboard views

### Assets
**Location**: `resources/`

#### JavaScript
- `resources/js/app.js` - Main application JavaScript
- Vue.js components for interactive elements

#### Styles
- `resources/sass/app.scss` - Main application styles
- Bootstrap 4 for responsive design

---

## Configuration

### Environment Variables
```env
# Database
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=ecommerce
DB_USERNAME=root
DB_PASSWORD=

# PayPal Configuration
PAYPAL_CLIENT_ID=your_paypal_client_id
PAYPAL_CLIENT_SECRET=your_paypal_secret
PAYPAL_MODE=sandbox # or live

# Mail Configuration
MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=null
MAIL_PASSWORD=null

# Newsletter (MailChimp)
MAILCHIMP_APIKEY=your_mailchimp_api_key
MAILCHIMP_LIST_ID=your_list_id
```

### Key Configuration Files
- `config/app.php` - Application settings
- `config/database.php` - Database configuration
- `config/paypal.php` - PayPal payment settings
- `config/newsletter.php` - Newsletter/MailChimp settings

---

## Setup Instructions

### Installation

1. **Clone and Install Dependencies**
```bash
git clone <repository>
cd ecommerce-app
composer install
npm install
```

2. **Environment Configuration**
```bash
cp .env.example .env
php artisan key:generate
```

3. **Database Setup**
```bash
php artisan migrate
php artisan db:seed # If seeders are available
```

4. **Storage and Permissions**
```bash
php artisan storage:link
chmod -R 775 storage bootstrap/cache
```

5. **Asset Compilation**
```bash
npm run dev # Development
npm run prod # Production
```

6. **File Manager Setup**
```bash
php artisan vendor:publish --tag=lfm_config
php artisan vendor:publish --tag=lfm_public
```

### Development Commands

```bash
# Start development server
php artisan serve

# Watch for asset changes
npm run watch

# Clear caches
php artisan cache:clear
php artisan config:clear
php artisan view:clear
```

---

## Examples and Usage

### Adding Products to Cart

```javascript
// Frontend JavaScript example
function addToCart(productSlug) {
    fetch(`/add-to-cart/${productSlug}`, {
        method: 'GET',
        headers: {
            'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').content
        }
    })
    .then(response => response.json())
    .then(data => {
        if (data.success) {
            updateCartCount();
            showSuccessMessage('Product added to cart');
        }
    });
}
```

### Product Search Implementation

```php
// In a custom controller
public function searchProducts(Request $request)
{
    $query = $request->input('search');
    
    $products = Product::where('status', 'active')
        ->where(function($q) use ($query) {
            $q->where('title', 'like', "%{$query}%")
              ->orWhere('description', 'like', "%{$query}%")
              ->orWhere('summary', 'like', "%{$query}%");
        })
        ->with(['cat_info', 'getReview'])
        ->paginate(12);
    
    return view('frontend.pages.search-results', compact('products', 'query'));
}
```

### Custom Cart Operations

```php
// Add multiple products to cart
public function bulkAddToCart(Request $request)
{
    $products = $request->input('products'); // Array of product IDs and quantities
    
    foreach ($products as $productData) {
        $product = Product::find($productData['id']);
        
        if ($product && $product->stock >= $productData['quantity']) {
            Cart::create([
                'user_id' => auth()->id(),
                'product_id' => $product->id,
                'quantity' => $productData['quantity'],
                'price' => $product->price,
                'amount' => $product->price * $productData['quantity']
            ]);
        }
    }
    
    return response()->json(['success' => true]);
}
```

### Order Processing Example

```php
// Process order after payment confirmation
public function processOrder(Request $request)
{
    $cartItems = Helper::getAllProductFromCart(auth()->id());
    $total = Helper::totalCartPrice(auth()->id());
    
    $order = Order::create([
        'user_id' => auth()->id(),
        'order_number' => 'ORD-' . time(),
        'sub_total' => $total,
        'quantity' => $cartItems->sum('quantity'),
        'total_amount' => $total + $request->shipping_cost,
        'status' => 'pending',
        'payment_status' => 'completed'
        // ... other order fields
    ]);
    
    // Associate cart items with order
    Cart::where('user_id', auth()->id())
        ->whereNull('order_id')
        ->update(['order_id' => $order->id]);
    
    return redirect()->route('order.success', $order->id);
}
```

### Category Hierarchy Display

```php
// Display nested categories in views
@foreach(Helper::getAllCategory() as $category)
    <div class="category">
        <h3>{{ $category->title }}</h3>
        @if($category->child_cat->count() > 0)
            <ul class="subcategories">
                @foreach($category->child_cat as $subcategory)
                    <li><a href="{{ route('product-sub-cat', [$category->slug, $subcategory->slug]) }}">
                        {{ $subcategory->title }}
                    </a></li>
                @endforeach
            </ul>
        @endif
    </div>
@endforeach
```

---

## Error Handling

### Common Error Responses

```php
// Product not found
return back()->with('error', 'Product not found');

// Insufficient stock
return back()->with('error', 'Stock not sufficient!');

// Authentication required
return redirect()->route('login')->with('error', 'Please login to continue');

// Success responses
return back()->with('success', 'Product added to cart successfully');
```

### API Error Format

```json
{
    "success": false,
    "message": "Validation failed",
    "errors": {
        "email": ["The email field is required."],
        "password": ["The password field is required."]
    }
}
```

---

This documentation provides comprehensive coverage of all public APIs, functions, and components in your Laravel e-commerce application. Each section includes practical examples and usage instructions to help developers understand and implement the functionality effectively.