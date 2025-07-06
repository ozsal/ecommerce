# E-Commerce Laravel Application - Flowchart Diagrams

## Table of Contents
1. [Main User Journey Flowchart](#main-user-journey-flowchart)
2. [Shopping Cart Process Flowchart](#shopping-cart-process-flowchart)
3. [Order Processing Flowchart](#order-processing-flowchart)
4. [User Authentication Flowchart](#user-authentication-flowchart)
5. [Admin Management Flowchart](#admin-management-flowchart)
6. [Product Management Flowchart](#product-management-flowchart)
7. [Payment Processing Flowchart](#payment-processing-flowchart)

---

## Main User Journey Flowchart

```mermaid
flowchart TD
    START([🏁 User Visits Website]) --> HOME[🏠 Homepage Display]
    HOME --> BROWSE{🔍 Browse Products?}
    
    BROWSE -->|Yes| CATALOG[📦 Product Catalog]
    BROWSE -->|No| INFO[ℹ️ About/Contact/Blog]
    
    CATALOG --> FILTER[🔧 Apply Filters<br/>Category/Brand/Price]
    FILTER --> PRODUCT[📱 Product Detail Page]
    
    PRODUCT --> AUTH_CHECK{🔐 User Logged In?}
    
    AUTH_CHECK -->|No| LOGIN_PROMPT[🚪 Login Required]
    AUTH_CHECK -->|Yes| ACTIONS{⚡ User Action?}
    
    LOGIN_PROMPT --> LOGIN_FORM[📝 Login Form]
    LOGIN_FORM --> REGISTER_CHECK{👤 New User?}
    
    REGISTER_CHECK -->|Yes| REGISTER[📋 Registration Form]
    REGISTER_CHECK -->|No| LOGIN_SUBMIT[✅ Submit Login]
    
    REGISTER --> REG_VALIDATE[🔍 Validate Registration]
    REG_VALIDATE --> REG_SUCCESS{✅ Valid Data?}
    REG_SUCCESS -->|No| REG_ERROR[❌ Show Errors]
    REG_SUCCESS -->|Yes| CREATE_USER[👤 Create Account]
    
    LOGIN_SUBMIT --> LOGIN_VALIDATE[🔍 Validate Credentials]
    LOGIN_VALIDATE --> LOGIN_SUCCESS{✅ Valid Login?}
    LOGIN_SUCCESS -->|No| LOGIN_ERROR[❌ Invalid Credentials]
    LOGIN_SUCCESS -->|Yes| SET_SESSION[🔑 Set User Session]
    
    CREATE_USER --> SET_SESSION
    SET_SESSION --> ROLE_CHECK{👑 User Role?}
    
    ROLE_CHECK -->|Admin| ADMIN_DASH[🎛️ Admin Dashboard]
    ROLE_CHECK -->|User| USER_DASH[👤 User Dashboard]
    
    USER_DASH --> ACTIONS
    ACTIONS -->|Add to Cart| CART_ADD[🛒 Add to Cart]
    ACTIONS -->|Add to Wishlist| WISH_ADD[❤️ Add to Wishlist]
    ACTIONS -->|Write Review| REVIEW_ADD[⭐ Write Review]
    
    CART_ADD --> CART_CHECK[🔍 Validate Cart]
    CART_CHECK --> STOCK_CHECK{📦 Stock Available?}
    STOCK_CHECK -->|No| STOCK_ERROR[❌ Out of Stock]
    STOCK_CHECK -->|Yes| CART_SAVE[💾 Save to Cart]
    
    CART_SAVE --> CART_SUCCESS[✅ Added Successfully]
    CART_SUCCESS --> CONTINUE{🔄 Continue Shopping?}
    
    CONTINUE -->|Yes| CATALOG
    CONTINUE -->|No| CART_VIEW[🛒 View Cart]
    
    CART_VIEW --> CHECKOUT[💳 Proceed to Checkout]
    CHECKOUT --> ORDER_COMPLETE[🎉 Order Complete]
    
    REG_ERROR --> REGISTER
    LOGIN_ERROR --> LOGIN_FORM
    STOCK_ERROR --> PRODUCT
    INFO --> HOME
    
    style START fill:#e3f2fd
    style ORDER_COMPLETE fill:#e8f5e8
    style REG_ERROR fill:#ffebee
    style LOGIN_ERROR fill:#ffebee
    style STOCK_ERROR fill:#ffebee
```

---

## Shopping Cart Process Flowchart

```mermaid
flowchart TD
    START([🛒 Start Cart Process]) --> PRODUCT_PAGE[📱 Product Detail Page]
    PRODUCT_PAGE --> ADD_DECISION{➕ Add to Cart?}
    
    ADD_DECISION -->|Single Item| SINGLE_ADD[1️⃣ Quick Add]
    ADD_DECISION -->|Custom Quantity| CUSTOM_ADD[🔢 Custom Quantity]
    ADD_DECISION -->|No| CONTINUE_BROWSE[🔍 Continue Browsing]
    
    SINGLE_ADD --> AUTH_CHECK{🔐 Authenticated?}
    CUSTOM_ADD --> QUANTITY_INPUT[📝 Enter Quantity]
    QUANTITY_INPUT --> AUTH_CHECK
    
    AUTH_CHECK -->|No| LOGIN_REDIRECT[🚪 Redirect to Login]
    AUTH_CHECK -->|Yes| VALIDATE_PRODUCT[🔍 Validate Product]
    
    VALIDATE_PRODUCT --> PRODUCT_VALID{✅ Product Valid?}
    PRODUCT_VALID -->|No| INVALID_ERROR[❌ Invalid Product Error]
    PRODUCT_VALID -->|Yes| CHECK_EXISTING[🔍 Check Existing Cart Item]
    
    CHECK_EXISTING --> ITEM_EXISTS{📦 Item in Cart?}
    ITEM_EXISTS -->|Yes| UPDATE_QUANTITY[🔄 Update Quantity]
    ITEM_EXISTS -->|No| CREATE_NEW[➕ Create New Cart Item]
    
    UPDATE_QUANTITY --> STOCK_VALIDATION[📦 Validate Stock]
    CREATE_NEW --> STOCK_VALIDATION
    
    STOCK_VALIDATION --> STOCK_AVAILABLE{📦 Stock Available?}
    STOCK_AVAILABLE -->|No| STOCK_ERROR[❌ Insufficient Stock]
    STOCK_AVAILABLE -->|Yes| SAVE_CART[💾 Save to Database]
    
    SAVE_CART --> UPDATE_WISHLIST[❤️ Update Wishlist]
    UPDATE_WISHLIST --> SUCCESS_MESSAGE[✅ Success Message]
    
    SUCCESS_MESSAGE --> CART_ACTIONS{🎯 Next Action?}
    CART_ACTIONS -->|View Cart| VIEW_CART[🛒 View Cart Page]
    CART_ACTIONS -->|Continue Shopping| CONTINUE_BROWSE
    CART_ACTIONS -->|Checkout| PROCEED_CHECKOUT[💳 Proceed to Checkout]
    
    VIEW_CART --> CART_MANAGEMENT{⚙️ Cart Management?}
    CART_MANAGEMENT -->|Update Quantities| UPDATE_CART[🔄 Update Cart]
    CART_MANAGEMENT -->|Remove Items| DELETE_ITEM[🗑️ Delete Item]
    CART_MANAGEMENT -->|Apply Coupon| APPLY_COUPON[🎫 Apply Coupon]
    CART_MANAGEMENT -->|Checkout| PROCEED_CHECKOUT
    
    UPDATE_CART --> VALIDATE_UPDATES[🔍 Validate Updates]
    VALIDATE_UPDATES --> UPDATE_SUCCESS[✅ Update Success]
    
    DELETE_ITEM --> CONFIRM_DELETE{❓ Confirm Delete?}
    CONFIRM_DELETE -->|Yes| REMOVE_FROM_DB[🗑️ Remove from Database]
    CONFIRM_DELETE -->|No| VIEW_CART
    
    APPLY_COUPON --> VALIDATE_COUPON[🔍 Validate Coupon]
    VALIDATE_COUPON --> COUPON_VALID{✅ Valid Coupon?}
    COUPON_VALID -->|Yes| APPLY_DISCOUNT[💰 Apply Discount]
    COUPON_VALID -->|No| COUPON_ERROR[❌ Invalid Coupon]
    
    PROCEED_CHECKOUT --> CHECKOUT_PROCESS[💳 Checkout Process]
    
    LOGIN_REDIRECT --> PRODUCT_PAGE
    INVALID_ERROR --> PRODUCT_PAGE
    STOCK_ERROR --> PRODUCT_PAGE
    UPDATE_SUCCESS --> VIEW_CART
    REMOVE_FROM_DB --> VIEW_CART
    APPLY_DISCOUNT --> VIEW_CART
    COUPON_ERROR --> VIEW_CART
    
    style START fill:#e3f2fd
    style SUCCESS_MESSAGE fill:#e8f5e8
    style CHECKOUT_PROCESS fill:#e8f5e8
    style INVALID_ERROR fill:#ffebee
    style STOCK_ERROR fill:#ffebee
    style COUPON_ERROR fill:#ffebee
```

---

## Order Processing Flowchart

```mermaid
flowchart TD
    START([💳 Start Checkout]) --> VALIDATE_CART[🔍 Validate Cart Items]
    VALIDATE_CART --> CART_VALID{✅ Cart Valid?}
    
    CART_VALID -->|No| CART_ERROR[❌ Cart Validation Error]
    CART_VALID -->|Yes| CHECKOUT_FORM[📝 Checkout Form]
    
    CHECKOUT_FORM --> FORM_INPUT[📋 User Input:<br/>- Shipping Address<br/>- Payment Method<br/>- Contact Info]
    FORM_INPUT --> SUBMIT_ORDER[📤 Submit Order]
    
    SUBMIT_ORDER --> VALIDATE_ORDER[🔍 Validate Order Data]
    VALIDATE_ORDER --> ORDER_VALID{✅ Order Valid?}
    
    ORDER_VALID -->|No| VALIDATION_ERROR[❌ Validation Errors]
    ORDER_VALID -->|Yes| CREATE_ORDER[📦 Create Order Record]
    
    CREATE_ORDER --> GENERATE_ORDER_NUMBER[🔢 Generate Order Number]
    GENERATE_ORDER_NUMBER --> CALCULATE_TOTALS[🧮 Calculate Totals:<br/>- Subtotal<br/>- Shipping<br/>- Tax<br/>- Discount]
    
    CALCULATE_TOTALS --> SAVE_ORDER[💾 Save Order to Database]
    SAVE_ORDER --> LINK_CART_ITEMS[🔗 Link Cart Items to Order]
    
    LINK_CART_ITEMS --> PAYMENT_METHOD{💰 Payment Method?}
    
    PAYMENT_METHOD -->|PayPal| PAYPAL_REDIRECT[🌐 Redirect to PayPal]
    PAYMENT_METHOD -->|Cash on Delivery| COD_PROCESS[💵 COD Processing]
    PAYMENT_METHOD -->|Credit Card| CARD_PROCESS[💳 Card Processing]
    
    PAYPAL_REDIRECT --> PAYPAL_PROCESSING[⏳ PayPal Processing]
    PAYPAL_PROCESSING --> PAYMENT_RESULT{💰 Payment Result?}
    
    PAYMENT_RESULT -->|Success| PAYMENT_SUCCESS[✅ Payment Success]
    PAYMENT_RESULT -->|Cancelled| PAYMENT_CANCELLED[❌ Payment Cancelled]
    PAYMENT_RESULT -->|Failed| PAYMENT_FAILED[❌ Payment Failed]
    
    COD_PROCESS --> UPDATE_ORDER_PENDING[📝 Update Order Status: Pending]
    CARD_PROCESS --> PAYMENT_SUCCESS
    
    PAYMENT_SUCCESS --> UPDATE_ORDER_PAID[📝 Update Order Status: Paid]
    UPDATE_ORDER_PAID --> REDUCE_STOCK[📦 Reduce Product Stock]
    UPDATE_ORDER_PENDING --> REDUCE_STOCK
    
    REDUCE_STOCK --> SEND_CONFIRMATION[📧 Send Order Confirmation Email]
    SEND_CONFIRMATION --> GENERATE_PDF[📄 Generate Order PDF]
    GENERATE_PDF --> CLEAR_CART[🗑️ Clear User Cart]
    
    CLEAR_CART --> ORDER_SUCCESS[🎉 Order Success Page]
    ORDER_SUCCESS --> ENABLE_TRACKING[🔍 Enable Order Tracking]
    
    PAYMENT_CANCELLED --> RESTORE_CART[🔄 Restore Cart Items]
    PAYMENT_FAILED --> RESTORE_CART
    RESTORE_CART --> CHECKOUT_FORM
    
    VALIDATION_ERROR --> CHECKOUT_FORM
    CART_ERROR --> VALIDATE_CART
    
    ENABLE_TRACKING --> END([🏁 Process Complete])
    
    style START fill:#e3f2fd
    style ORDER_SUCCESS fill:#e8f5e8
    style END fill:#e8f5e8
    style CART_ERROR fill:#ffebee
    style VALIDATION_ERROR fill:#ffebee
    style PAYMENT_CANCELLED fill:#ffebee
    style PAYMENT_FAILED fill:#ffebee
```

---

## User Authentication Flowchart

```mermaid
flowchart TD
    START([🚪 Authentication Start]) --> ACCESS_ATTEMPT[🔐 Access Protected Resource]
    ACCESS_ATTEMPT --> AUTH_CHECK{🔍 User Authenticated?}
    
    AUTH_CHECK -->|Yes| ROLE_CHECK{👑 Check User Role}
    AUTH_CHECK -->|No| LOGIN_PAGE[📝 Display Login Page]
    
    LOGIN_PAGE --> USER_CHOICE{👤 User Choice?}
    USER_CHOICE -->|Login| LOGIN_FORM[📝 Login Form]
    USER_CHOICE -->|Register| REGISTER_FORM[📋 Registration Form]
    USER_CHOICE -->|Social Login| SOCIAL_LOGIN[🌐 Social Login Options]
    
    LOGIN_FORM --> LOGIN_INPUT[📝 Enter Credentials:<br/>- Email<br/>- Password]
    LOGIN_INPUT --> LOGIN_SUBMIT[📤 Submit Login]
    
    LOGIN_SUBMIT --> VALIDATE_CREDENTIALS[🔍 Validate Credentials]
    VALIDATE_CREDENTIALS --> CREDENTIALS_VALID{✅ Valid Credentials?}
    
    CREDENTIALS_VALID -->|No| LOGIN_ERROR[❌ Invalid Email/Password]
    CREDENTIALS_VALID -->|Yes| CHECK_STATUS[🔍 Check Account Status]
    
    CHECK_STATUS --> STATUS_ACTIVE{✅ Account Active?}
    STATUS_ACTIVE -->|No| ACCOUNT_INACTIVE[❌ Account Inactive]
    STATUS_ACTIVE -->|Yes| AUTHENTICATE_USER[🔑 Authenticate User]
    
    REGISTER_FORM --> REGISTER_INPUT[📝 Enter Details:<br/>- Name<br/>- Email<br/>- Password<br/>- Confirm Password]
    REGISTER_INPUT --> REGISTER_SUBMIT[📤 Submit Registration]
    
    REGISTER_SUBMIT --> VALIDATE_REGISTRATION[🔍 Validate Registration Data]
    VALIDATE_REGISTRATION --> REGISTRATION_VALID{✅ Valid Data?}
    
    REGISTRATION_VALID -->|No| REGISTRATION_ERROR[❌ Validation Errors]
    REGISTRATION_VALID -->|Yes| CREATE_ACCOUNT[👤 Create User Account]
    
    CREATE_ACCOUNT --> SEND_WELCOME[📧 Send Welcome Email]
    SEND_WELCOME --> AUTO_LOGIN[🔑 Auto Login User]
    
    SOCIAL_LOGIN --> SOCIAL_PROVIDER{🌐 Choose Provider?}
    SOCIAL_PROVIDER -->|Google| GOOGLE_AUTH[🔍 Google Authentication]
    SOCIAL_PROVIDER -->|Facebook| FACEBOOK_AUTH[📘 Facebook Authentication]
    
    GOOGLE_AUTH --> SOCIAL_CALLBACK[🔄 Social Login Callback]
    FACEBOOK_AUTH --> SOCIAL_CALLBACK
    
    SOCIAL_CALLBACK --> SOCIAL_SUCCESS{✅ Social Auth Success?}
    SOCIAL_SUCCESS -->|No| SOCIAL_ERROR[❌ Social Login Failed]
    SOCIAL_SUCCESS -->|Yes| LINK_ACCOUNT[🔗 Link/Create Account]
    
    AUTHENTICATE_USER --> SET_SESSION[🔑 Set User Session]
    AUTO_LOGIN --> SET_SESSION
    LINK_ACCOUNT --> SET_SESSION
    
    SET_SESSION --> ROLE_CHECK
    
    ROLE_CHECK -->|Admin| ADMIN_DASHBOARD[🎛️ Admin Dashboard]
    ROLE_CHECK -->|User| USER_DASHBOARD[👤 User Dashboard]
    ROLE_CHECK -->|Manager| MANAGER_DASHBOARD[📊 Manager Dashboard]
    
    ADMIN_DASHBOARD --> ADMIN_ACCESS[🛠️ Admin Features Access]
    USER_DASHBOARD --> USER_ACCESS[🛍️ User Features Access]
    MANAGER_DASHBOARD --> MANAGER_ACCESS[📈 Manager Features Access]
    
    LOGIN_ERROR --> LOGIN_FORM
    ACCOUNT_INACTIVE --> LOGIN_PAGE
    REGISTRATION_ERROR --> REGISTER_FORM
    SOCIAL_ERROR --> LOGIN_PAGE
    
    style START fill:#e3f2fd
    style ADMIN_ACCESS fill:#f3e5f5
    style USER_ACCESS fill:#e8f5e8
    style MANAGER_ACCESS fill:#fff3e0
    style LOGIN_ERROR fill:#ffebee
    style ACCOUNT_INACTIVE fill:#ffebee
    style REGISTRATION_ERROR fill:#ffebee
    style SOCIAL_ERROR fill:#ffebee
```

---

## Admin Management Flowchart

```mermaid
flowchart TD
    START([🎛️ Admin Login]) --> ADMIN_AUTH[🔐 Admin Authentication]
    ADMIN_AUTH --> AUTH_SUCCESS{✅ Authentication Success?}
    
    AUTH_SUCCESS -->|No| AUTH_ERROR[❌ Authentication Failed]
    AUTH_SUCCESS -->|Yes| ADMIN_DASHBOARD[🎛️ Admin Dashboard]
    
    ADMIN_DASHBOARD --> ADMIN_CHOICE{⚙️ Management Choice?}
    
    ADMIN_CHOICE -->|Users| USER_MGMT[👥 User Management]
    ADMIN_CHOICE -->|Products| PRODUCT_MGMT[📦 Product Management]
    ADMIN_CHOICE -->|Orders| ORDER_MGMT[📋 Order Management]
    ADMIN_CHOICE -->|Content| CONTENT_MGMT[📝 Content Management]
    ADMIN_CHOICE -->|Settings| SETTINGS_MGMT[⚙️ Settings Management]
    ADMIN_CHOICE -->|Reports| REPORTS[📊 Reports & Analytics]
    
    USER_MGMT --> USER_ACTION{👤 User Action?}
    USER_ACTION -->|Create| CREATE_USER[➕ Create New User]
    USER_ACTION -->|Edit| EDIT_USER[✏️ Edit User]
    USER_ACTION -->|Delete| DELETE_USER[🗑️ Delete User]
    USER_ACTION -->|View| VIEW_USERS[👀 View All Users]
    
    CREATE_USER --> USER_FORM[📝 User Creation Form]
    USER_FORM --> VALIDATE_USER[🔍 Validate User Data]
    VALIDATE_USER --> USER_VALID{✅ Valid Data?}
    USER_VALID -->|No| USER_ERROR[❌ Validation Error]
    USER_VALID -->|Yes| SAVE_USER[💾 Save User]
    
    PRODUCT_MGMT --> PRODUCT_ACTION{📦 Product Action?}
    PRODUCT_ACTION -->|Create| CREATE_PRODUCT[➕ Create Product]
    PRODUCT_ACTION -->|Edit| EDIT_PRODUCT[✏️ Edit Product]
    PRODUCT_ACTION -->|Delete| DELETE_PRODUCT[🗑️ Delete Product]
    PRODUCT_ACTION -->|Categories| MANAGE_CATEGORIES[📂 Manage Categories]
    PRODUCT_ACTION -->|Brands| MANAGE_BRANDS[🏷️ Manage Brands]
    
    ORDER_MGMT --> ORDER_ACTION{📋 Order Action?}
    ORDER_ACTION -->|View All| VIEW_ORDERS[👀 View All Orders]
    ORDER_ACTION -->|Update Status| UPDATE_STATUS[🔄 Update Order Status]
    ORDER_ACTION -->|View Details| ORDER_DETAILS[📋 Order Details]
    ORDER_ACTION -->|Shipping| MANAGE_SHIPPING[🚚 Manage Shipping]
    
    UPDATE_STATUS --> STATUS_CHOICE{📦 New Status?}
    STATUS_CHOICE -->|Processing| SET_PROCESSING[⏳ Set Processing]
    STATUS_CHOICE -->|Shipped| SET_SHIPPED[🚚 Set Shipped]
    STATUS_CHOICE -->|Delivered| SET_DELIVERED[✅ Set Delivered]
    STATUS_CHOICE -->|Cancelled| SET_CANCELLED[❌ Set Cancelled]
    
    SET_PROCESSING --> NOTIFY_CUSTOMER[📧 Notify Customer]
    SET_SHIPPED --> NOTIFY_CUSTOMER
    SET_DELIVERED --> NOTIFY_CUSTOMER
    SET_CANCELLED --> NOTIFY_CUSTOMER
    
    CONTENT_MGMT --> CONTENT_ACTION{📝 Content Action?}
    CONTENT_ACTION -->|Blog Posts| MANAGE_POSTS[📝 Manage Blog Posts]
    CONTENT_ACTION -->|Banners| MANAGE_BANNERS[🖼️ Manage Banners]
    CONTENT_ACTION -->|Coupons| MANAGE_COUPONS[🎫 Manage Coupons]
    CONTENT_ACTION -->|Messages| MANAGE_MESSAGES[💬 Manage Messages]
    
    SETTINGS_MGMT --> SETTINGS_ACTION{⚙️ Settings Action?}
    SETTINGS_ACTION -->|App Settings| APP_SETTINGS[🔧 Application Settings]
    SETTINGS_ACTION -->|Payment| PAYMENT_SETTINGS[💳 Payment Settings]
    SETTINGS_ACTION -->|Email| EMAIL_SETTINGS[📧 Email Settings]
    SETTINGS_ACTION -->|Profile| ADMIN_PROFILE[👤 Admin Profile]
    
    REPORTS --> REPORT_TYPE{📊 Report Type?}
    REPORT_TYPE -->|Sales| SALES_REPORT[💰 Sales Report]
    REPORT_TYPE -->|Users| USER_REPORT[👥 User Report]
    REPORT_TYPE -->|Products| PRODUCT_REPORT[📦 Product Report]
    REPORT_TYPE -->|Orders| ORDER_REPORT[📋 Order Report]
    
    SAVE_USER --> SUCCESS_MESSAGE[✅ Success Message]
    NOTIFY_CUSTOMER --> SUCCESS_MESSAGE
    SUCCESS_MESSAGE --> ADMIN_DASHBOARD
    
    USER_ERROR --> USER_FORM
    AUTH_ERROR --> START
    
    style START fill:#e3f2fd
    style ADMIN_DASHBOARD fill:#f3e5f5
    style SUCCESS_MESSAGE fill:#e8f5e8
    style AUTH_ERROR fill:#ffebee
    style USER_ERROR fill:#ffebee
```

---

## Product Management Flowchart

```mermaid
flowchart TD
    START([📦 Product Management]) --> PRODUCT_DASHBOARD[📦 Product Dashboard]
    PRODUCT_DASHBOARD --> ACTION_CHOICE{⚡ Action Choice?}
    
    ACTION_CHOICE -->|Create| CREATE_PRODUCT[➕ Create New Product]
    ACTION_CHOICE -->|Edit| SELECT_PRODUCT[🔍 Select Product to Edit]
    ACTION_CHOICE -->|Delete| DELETE_PRODUCT[🗑️ Delete Product]
    ACTION_CHOICE -->|View| VIEW_PRODUCTS[👀 View All Products]
    ACTION_CHOICE -->|Categories| CATEGORY_MGMT[📂 Category Management]
    ACTION_CHOICE -->|Brands| BRAND_MGMT[🏷️ Brand Management]
    
    CREATE_PRODUCT --> PRODUCT_FORM[📝 Product Creation Form]
    PRODUCT_FORM --> FORM_INPUT[📋 Enter Product Details:<br/>- Title & Description<br/>- Price & Discount<br/>- Category & Brand<br/>- Stock & Images]
    
    FORM_INPUT --> UPLOAD_IMAGES[🖼️ Upload Product Images]
    UPLOAD_IMAGES --> VALIDATE_PRODUCT[🔍 Validate Product Data]
    
    VALIDATE_PRODUCT --> PRODUCT_VALID{✅ Valid Product Data?}
    PRODUCT_VALID -->|No| VALIDATION_ERROR[❌ Validation Errors]
    PRODUCT_VALID -->|Yes| GENERATE_SLUG[🔗 Generate Product Slug]
    
    GENERATE_SLUG --> SAVE_PRODUCT[💾 Save Product to Database]
    SAVE_PRODUCT --> PRODUCT_SUCCESS[✅ Product Created Successfully]
    
    SELECT_PRODUCT --> EDIT_FORM[✏️ Edit Product Form]
    EDIT_FORM --> UPDATE_INPUT[📋 Update Product Details]
    UPDATE_INPUT --> UPDATE_IMAGES{🖼️ Update Images?}
    
    UPDATE_IMAGES -->|Yes| NEW_IMAGES[📸 Upload New Images]
    UPDATE_IMAGES -->|No| VALIDATE_UPDATE[🔍 Validate Updates]
    
    NEW_IMAGES --> VALIDATE_UPDATE
    VALIDATE_UPDATE --> UPDATE_VALID{✅ Valid Updates?}
    
    UPDATE_VALID -->|No| UPDATE_ERROR[❌ Update Validation Error]
    UPDATE_VALID -->|Yes| SAVE_UPDATES[💾 Save Updates]
    SAVE_UPDATES --> UPDATE_SUCCESS[✅ Product Updated Successfully]
    
    DELETE_PRODUCT --> CONFIRM_DELETE{❓ Confirm Deletion?}
    CONFIRM_DELETE -->|No| PRODUCT_DASHBOARD
    CONFIRM_DELETE -->|Yes| CHECK_DEPENDENCIES[🔍 Check Dependencies]
    
    CHECK_DEPENDENCIES --> HAS_ORDERS{📋 Has Active Orders?}
    HAS_ORDERS -->|Yes| CANNOT_DELETE[❌ Cannot Delete:<br/>Product has orders]
    HAS_ORDERS -->|No| REMOVE_PRODUCT[🗑️ Remove from Database]
    
    REMOVE_PRODUCT --> DELETE_SUCCESS[✅ Product Deleted Successfully]
    
    CATEGORY_MGMT --> CATEGORY_ACTION{📂 Category Action?}
    CATEGORY_ACTION -->|Create| CREATE_CATEGORY[➕ Create Category]
    CATEGORY_ACTION -->|Edit| EDIT_CATEGORY[✏️ Edit Category]
    CATEGORY_ACTION -->|Delete| DELETE_CATEGORY[🗑️ Delete Category]
    
    CREATE_CATEGORY --> CATEGORY_FORM[📝 Category Form]
    CATEGORY_FORM --> PARENT_CHOICE{👆 Parent Category?}
    PARENT_CHOICE -->|Yes| SELECT_PARENT[📂 Select Parent Category]
    PARENT_CHOICE -->|No| ROOT_CATEGORY[🌳 Create Root Category]
    
    SELECT_PARENT --> SAVE_CATEGORY[💾 Save Category]
    ROOT_CATEGORY --> SAVE_CATEGORY
    SAVE_CATEGORY --> CATEGORY_SUCCESS[✅ Category Created]
    
    BRAND_MGMT --> BRAND_ACTION{🏷️ Brand Action?}
    BRAND_ACTION -->|Create| CREATE_BRAND[➕ Create Brand]
    BRAND_ACTION -->|Edit| EDIT_BRAND[✏️ Edit Brand]
    BRAND_ACTION -->|Delete| DELETE_BRAND[🗑️ Delete Brand]
    
    CREATE_BRAND --> BRAND_FORM[📝 Brand Form]
    BRAND_FORM --> SAVE_BRAND[💾 Save Brand]
    SAVE_BRAND --> BRAND_SUCCESS[✅ Brand Created]
    
    PRODUCT_SUCCESS --> PRODUCT_DASHBOARD
    UPDATE_SUCCESS --> PRODUCT_DASHBOARD
    DELETE_SUCCESS --> PRODUCT_DASHBOARD
    CATEGORY_SUCCESS --> CATEGORY_MGMT
    BRAND_SUCCESS --> BRAND_MGMT
    
    VALIDATION_ERROR --> PRODUCT_FORM
    UPDATE_ERROR --> EDIT_FORM
    CANNOT_DELETE --> PRODUCT_DASHBOARD
    
    style START fill:#e3f2fd
    style PRODUCT_SUCCESS fill:#e8f5e8
    style UPDATE_SUCCESS fill:#e8f5e8
    style DELETE_SUCCESS fill:#e8f5e8
    style CATEGORY_SUCCESS fill:#e8f5e8
    style BRAND_SUCCESS fill:#e8f5e8
    style VALIDATION_ERROR fill:#ffebee
    style UPDATE_ERROR fill:#ffebee
    style CANNOT_DELETE fill:#ffebee
```

---

## Payment Processing Flowchart

```mermaid
flowchart TD
    START([💳 Payment Process]) --> PAYMENT_SELECTION[💰 Payment Method Selection]
    PAYMENT_SELECTION --> METHOD_CHOICE{💳 Payment Method?}
    
    METHOD_CHOICE -->|PayPal| PAYPAL_FLOW[🌐 PayPal Payment Flow]
    METHOD_CHOICE -->|Credit Card| CARD_FLOW[💳 Credit Card Flow]
    METHOD_CHOICE -->|Cash on Delivery| COD_FLOW[💵 COD Flow]
    METHOD_CHOICE -->|Bank Transfer| BANK_FLOW[🏦 Bank Transfer Flow]
    
    PAYPAL_FLOW --> PAYPAL_REDIRECT[🌐 Redirect to PayPal]
    PAYPAL_REDIRECT --> PAYPAL_LOGIN[🔐 PayPal Login]
    PAYPAL_LOGIN --> PAYPAL_AUTHORIZE[✅ Authorize Payment]
    
    PAYPAL_AUTHORIZE --> PAYPAL_RESULT{💰 PayPal Result?}
    PAYPAL_RESULT -->|Success| PAYPAL_SUCCESS[✅ PayPal Payment Success]
    PAYPAL_RESULT -->|Cancelled| PAYPAL_CANCEL[❌ Payment Cancelled]
    PAYPAL_RESULT -->|Failed| PAYPAL_FAIL[❌ Payment Failed]
    
    CARD_FLOW --> CARD_INPUT[💳 Enter Card Details:<br/>- Card Number<br/>- Expiry Date<br/>- CVV<br/>- Cardholder Name]
    CARD_INPUT --> VALIDATE_CARD[🔍 Validate Card Details]
    
    VALIDATE_CARD --> CARD_VALID{✅ Valid Card?}
    CARD_VALID -->|No| CARD_ERROR[❌ Invalid Card Details]
    CARD_VALID -->|Yes| PROCESS_CARD[⏳ Process Card Payment]
    
    PROCESS_CARD --> GATEWAY_RESPONSE{🏦 Gateway Response?}
    GATEWAY_RESPONSE -->|Approved| CARD_SUCCESS[✅ Card Payment Success]
    GATEWAY_RESPONSE -->|Declined| CARD_DECLINED[❌ Card Declined]
    GATEWAY_RESPONSE -->|Error| CARD_FAIL[❌ Processing Error]
    
    COD_FLOW --> COD_CONFIRM[📋 Confirm COD Order]
    COD_CONFIRM --> COD_DETAILS[📝 COD Order Details:<br/>- Delivery Address<br/>- Contact Number<br/>- Payment Amount]
    COD_DETAILS --> COD_SUCCESS[✅ COD Order Placed]
    
    BANK_FLOW --> BANK_DETAILS[🏦 Bank Transfer Details]
    BANK_DETAILS --> BANK_INSTRUCTIONS[📋 Transfer Instructions:<br/>- Account Number<br/>- Reference Number<br/>- Amount]
    BANK_INSTRUCTIONS --> BANK_PENDING[⏳ Awaiting Bank Transfer]
    
    PAYPAL_SUCCESS --> UPDATE_ORDER_PAID[📝 Update Order Status: Paid]
    CARD_SUCCESS --> UPDATE_ORDER_PAID
    
    COD_SUCCESS --> UPDATE_ORDER_PENDING[📝 Update Order Status: Pending]
    BANK_PENDING --> UPDATE_ORDER_PENDING
    
    UPDATE_ORDER_PAID --> PAYMENT_CONFIRMATION[📧 Send Payment Confirmation]
    UPDATE_ORDER_PENDING --> ORDER_CONFIRMATION[📧 Send Order Confirmation]
    
    PAYMENT_CONFIRMATION --> PROCESS_ORDER[📦 Process Order for Shipping]
    ORDER_CONFIRMATION --> AWAIT_PAYMENT[⏳ Await Payment Confirmation]
    
    PROCESS_ORDER --> REDUCE_INVENTORY[📦 Reduce Product Inventory]
    REDUCE_INVENTORY --> GENERATE_INVOICE[🧾 Generate Invoice]
    GENERATE_INVOICE --> PAYMENT_SUCCESS_PAGE[🎉 Payment Success Page]
    
    PAYPAL_CANCEL --> RESTORE_CART[🔄 Restore Shopping Cart]
    PAYPAL_FAIL --> RESTORE_CART
    CARD_DECLINED --> RESTORE_CART
    CARD_FAIL --> RESTORE_CART
    
    RESTORE_CART --> PAYMENT_SELECTION
    CARD_ERROR --> CARD_INPUT
    
    AWAIT_PAYMENT --> MANUAL_VERIFICATION[👤 Manual Payment Verification]
    MANUAL_VERIFICATION --> VERIFICATION_RESULT{✅ Payment Verified?}
    VERIFICATION_RESULT -->|Yes| UPDATE_ORDER_PAID
    VERIFICATION_RESULT -->|No| PAYMENT_REJECTED[❌ Payment Rejected]
    
    PAYMENT_REJECTED --> CANCEL_ORDER[❌ Cancel Order]
    CANCEL_ORDER --> RESTORE_INVENTORY[📦 Restore Inventory]
    
    style START fill:#e3f2fd
    style PAYMENT_SUCCESS_PAGE fill:#e8f5e8
    style PAYPAL_SUCCESS fill:#e8f5e8
    style CARD_SUCCESS fill:#e8f5e8
    style COD_SUCCESS fill:#e8f5e8
    style PAYPAL_CANCEL fill:#ffebee
    style PAYPAL_FAIL fill:#ffebee
    style CARD_ERROR fill:#ffebee
    style CARD_DECLINED fill:#ffebee
    style CARD_FAIL fill:#ffebee
    style PAYMENT_REJECTED fill:#ffebee
```

---

## Legend

### Flowchart Symbols Used:
- 🏁 **Oval**: Start/End points
- 📝 **Rectangle**: Process/Action steps
- 💎 **Diamond**: Decision points
- 📋 **Parallelogram**: Input/Output operations
- 🔄 **Circle**: Connectors/References
- ➡️ **Arrow**: Flow direction

### Color Coding:
- 🔵 **Blue**: Start points and information
- 🟢 **Green**: Success states and completion
- 🔴 **Red**: Error states and failures
- 🟡 **Yellow**: Warning or pending states
- 🟣 **Purple**: Admin-specific processes

---

These comprehensive flowchart diagrams provide a complete visual representation of all major processes in your Laravel e-commerce application, using proper flowchart symbols and clear decision paths.