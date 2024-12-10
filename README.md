# Schema Definitions

## With Moogose ODM

### User
```javascript
const mongoose = require("mongoose");

const UserSchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
    email: { type: String, unique: true, required: true },
    password: { type: String },
    emailVerified: { type: Date },
    isEmailVerified: { type: Boolean, default: false },
    image: { type: String },
    role: { type: String, enum: ["User", "Admin", "Supplier"], default: "User" },
    isAccountActive: { type: Boolean, default: true },
    verificationCode: { type: String },
    resetPasswordToken: { type: String, unique: true },
    resetPasswordExpires: { type: Date },
  },
  {
  collection: "User",
  timestamps: true
 }
);

const User = mongoose.model("User", UserSchema);
module.exports = { User };
```

### Supplier
```javascript
const SupplierSchema = new mongoose.Schema(
  {
    userId: { type: mongoose.Schema.Types.ObjectId, ref: "User", unique: true },
    contactEmail: { type: String },
    contactPhone: { type: String, required: true },
    whatsAppNumber: { type: String },
    address: { type: String, required: true },
    city: { type: String, required: true },
    bio: { type: String },
    serviceType: [{ type: String, enum: ["Print", "Design", "Brand", "Stationary", "Bidding", "Equipment"] }],
  },
  {
  collection: "Supplier",
  timestamps: true
 }
);

const Supplier = mongoose.model("Supplier", SupplierSchema);
module.exports = { Supplier };
```

### Verification
```javascript
const VerificationSchema = new mongoose.Schema(
  {
    supplierId: { type: mongoose.Schema.Types.ObjectId, ref: "Supplier", unique: true },
    status: { type: String, enum: ["UnVerified", "Pending", "Verified", "Rejected"], default: "UnVerified" },
    documentUrl: { type: String },
    nationalId: { type: String },
    physicalAddress: { type: String },
    verified: { type: Boolean, default: false },
    isDocumentVerified: { type: Boolean, default: false },
    isNationIDVerified: { type: Boolean, default: false },
    isPhysicalAddressVerified: { type: Boolean, default: false },
    isSocialMediaVerified: { type: Boolean, default: false },
  },
  {
  collection: "Verification",
  timestamps: true
 }
);

const Verification = mongoose.model("Verification", VerificationSchema);
module.exports = { Verification };
```

### Order
```javascript
const OrderSchema = new mongoose.Schema(
  {
    supplierId: { type: mongoose.Schema.Types.ObjectId, ref: "Supplier", required: true },
    userId: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
    equipmentId: { type: mongoose.Schema.Types.ObjectId, ref: "Equipment" },
    orderNumber: { type: String, unique: true, required: true },
    orderType: { type: String, enum: ["Print", "Design", "Brand", "Stationary", "Bidding", "Equipment"], required: true },
    status: { type: String, enum: ["Pending", "Confirmed", "Completed", "Delivered", "Cancelled"], default: "Pending" },
    totalAmount: { type: Number, required: true },
    metadata: { type: mongoose.Schema.Types.Mixed },
  },
  {
  collection: "Order",
  timestamps: true
 }
);

const Order = mongoose.model("Order", OrderSchema);
module.exports = { Order };
```

**Note:**
The metadata field will contain only data specific to that order depending on type e.g print order, design order, brand order, etc.

### Examples
**- Print Order Metadata**
```javascript
 metadata: {
  colorMode: "Black and White", // Options: "Black and White", "Color"
  paperType: "A4",             // Options: "A4", "Letter", "Legal"
  bindingType: "None",         // Options: "None", "Stapled", "Ring Binding", "Glue Binding"
  paperQuality: "Premium",     // Options: "Standard", "Premium", "Recycled"
  quantity: 200,               // Number of copies to print
  fileUrl: "https://example.com/document.pdf" // URL of the file to print
    }
```

**- Design Order Metadata**
```javascript
metadata: {
  designType: "Logo",                // Options: "Logo", "Flyer", "Banner", "Poster"
  dimensions: "1920x1080",          // Dimensions in pixels or inches
  colorPalette: ["#000000", "#FFFFFF"], // Array of HEX color codes
  fontPreference: "Sans-serif",     // Font type preferred
  revisionRounds: 3,                // Number of revision rounds allowed
  deadline: "2024-12-31T23:59:59Z"  // ISO date for the design completion deadline
}
```

**- Brand Order Metadata**
```javascript
metadata: {
  brandingMaterial: "T-shirts",     // Options: "T-shirts", "Caps", "Mugs", "Bags"
  quantity: 50,                     // Number of items to brand
  placement: "Front and Back",      // Options: "Front Only", "Back Only", "Front and Back"
  logoUrl: "https://example.com/logo.png", // URL of the logo to be used
  text: "Your Brand Slogan",        // Text to print on the items
  color: "Blue"                     // Color of the branding item
}
```

**- Stationary Order Metadata**
```javascript
metadata: {
  itemType: "Notebooks",            // Options: "Notebooks", "Pens", "Markers", "Folders"
  brand: "Camlin",                  // Brand of the item
  quantity: 200,                    // Number of units
  size: "A5",                       // Size of the stationery item
  coverMaterial: "Plastic",         // Options: "Paper", "Plastic", "Hardcover"
  priority: "High"                  // Priority level: "Low", "Medium", "High"
}
```

**- Document Bidding Order Metadata**
```javascript
metadata: {
  projectTitle: "Office Renovation",    // Title of the project
  budget: 50000,                        // Budget for the project
  bidDocumentUrl: "https://example.com/bid-doc.pdf", // URL of the bidding document
  deadline: "2024-12-20T17:00:00Z",     // Deadline for bid submission
  requiredExperience: "5 years",        // Required experience for bidders
  additionalNotes: "Include a breakdown of costs and timelines." // Notes for bidders
}
```

### Equipment
```javascript
const EquipmentSchema = new mongoose.Schema(
  {
    categoryId: { type: mongoose.Schema.Types.ObjectId, ref: "EquipmentCategory", required: true },
    userId: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
    name: { type: String, required: true },
    description: { type: String },
    price: { type: Number, required: true },
    status: { type: String, enum: ["Available", "Out_Of_Stock"], default: "Available" },
    stockQuantity: { type: Number, required: true },
    images: [{ type: String }],
  },
  {
  collection: "Equipment",
  timestamps: true
 }
);

const Equipment = mongoose.model("Equipment", EquipmentSchema);
module.exports = { EquipmentCategory };
```

### Equipment Category
```javascript
const EquipmentCategorySchema = new mongoose.Schema(
  {
    userId: { type: mongoose.Schema.Types.ObjectId, ref: "User", required: true },
    name: { type: String, required: true },
    description: { type: String },
    image: { type: String },
  },
  {
  collection: "EquipmentCategory",
  timestamps: true
 }
);

const EquipmentCategory = mongoose.model("EquipmentCategory", EquipmentCategorySchema);
module.exports = { EquipmentCategory };
```

### Order Interaction Model ( For Bidding $ Design Orders)
```javascript
const mongoose = require("mongoose");
const { Schema } = mongoose;

const OrderInteractionSchema = new Schema(
  {
    orderId: { type: Schema.Types.ObjectId, ref: "Order", required: true },
    senderId: { type: Schema.Types.ObjectId, ref: "User" }, // Optional, if sender is a user
    type: { type: String, enum: ["Message", "File"], required: true },
    content: { type: String }, // For messages or file descriptions
    fileUrl: { type: String }, // For design files or attached documents
  },
  {
  collection: "OrderInteraction",
  timestamps: true
 }
);

const OrderInteraction = mongoose.model("OrderInteraction", OrderInteractionSchema);
module.exports = { OrderInteraction };
```

---

## With Prisma ORM

```prisma
datasource db {
  provider = "mongodb"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

enum UserRole {
  User
  Admin
  Suppier
}

model User {
  id                   String    @id @default(uuid()) @map("_id") @db.ObjectId
  name                 String
  email                String    @unique
  password             String?
  emailVerified        DateTime?
  isEmailVerified      Boolean?  @default(false)
  image                String?
  role                 UserRole  @default(User)
  isAccountActive      Boolean   @default(true)
  verificationCode     String?
  resetPasswordToken   String?   @unique
  resetPasswordExpires DateTime?
  createdAt            DateTime  @default(now())
  updatedAt            DateTime  @updatedAt

  supplier Supplier?

  orders              Order[]
  equipments          Equipment[]
  equipmentCategories EquipmentCategory[]
  orderInteractions   OrderInteraction[]
}

enum ServiceType {
  Print
  Design
  Brand
  Stationary
  Bidding
  Equipment
}

model Supplier {
  id             String        @id @default(uuid()) @map("_id") @db.ObjectId
  userId         String        @unique @db.ObjectId
  contactEmail   String?
  contactPhone   String
  whatsAppNumber String?
  address        String
  city           String
  bio            String?
  serviceType    ServiceType[]
  createdAt      DateTime      @default(now())
  updatedAt      DateTime      @updatedAt

  verification Verification?
  user         User          @relation(fields: [userId], references: [id], onDelete: Cascade)

  orders Order[]
}

enum VerificationStatus {
  UnVerified
  Pending
  Verified
  Rejected
}

model Verification {
  id                        String             @id @default(uuid()) @map("_id")
  supplierId                String             @unique @db.ObjectId
  status                    VerificationStatus @default(UnVerified)
  documentUrl               String?
  nationalId                String?
  physicalAddress           String?
  verified                  Boolean            @default(false)
  isDocumentVerified        Boolean            @default(false)
  isNationIDVerified        Boolean            @default(false)
  isPhysicalAddressVerified Boolean            @default(false)
  isSocialMediaVerified     Boolean            @default(false)
  createdAt                 DateTime           @default(now())
  updatedAt                 DateTime           @updatedAt

  supplier Supplier @relation(fields: [supplierId], references: [id], onDelete: Cascade)
}

enum OrderType {
  Print
  Design
  Brand
  Stationary
  Bidding
  Equipment
}

enum OrderStatus {
  Pending
  Confirmed
  Completed
  Delivered
  Cancelled
}

model Order {
  id                 String      @id @default(uuid()) @map("_id") @db.ObjectId
  supplierId         String      @db.ObjectId
  userId             String      @db.ObjectId
  equipmentId        String?     @db.ObjectId
  orderInteractionId String?     @db.ObjectId
  orderNumber        String      @unique
  orderType          OrderType
  status             OrderStatus @default(Pending)
  totalAmount        Float
  metadata           Json
  createdAt          DateTime    @default(now())
  updatedAt          DateTime    @updatedAt

  supplier  Supplier   @relation(fields: [supplierId], references: [id], onDelete: Cascade)
  user      User       @relation(fields: [userId], references: [id], onDelete: Cascade)
  equipment Equipment? @relation(fields: [equipmentId], references: [id], onDelete: SetNull)

  orderInteractions OrderInteraction[]
}

enum EquipmentStatus {
  Available
  Out_Of_Stock
}

model Equipment {
  id            String          @id @default(uuid()) @map("_id") @db.ObjectId
  categoryId    String          @db.ObjectId
  userdId       String          @db.ObjectId
  name          String
  description   String?         @db.String
  price         Float
  status        EquipmentStatus @default(Available)
  stockQuantity Int
  images        String[]
  createdAt     DateTime        @default(now())
  updatedAt     DateTime        @updatedAt

  category EquipmentCategory @relation(fields: [categoryId], references: [id], onDelete: Cascade)
  user     User              @relation(fields: [userdId], references: [id], onDelete: Cascade)

  orders Order[]
}

model EquipmentCategory {
  id          String   @id @default(uuid()) @map("_id") @db.ObjectId
  userdId     String   @db.ObjectId
  name        String
  description String?
  image       String?
  createdAt   DateTime @default(now())
  updatedAt   DateTime @updatedAt

  user User @relation(fields: [userdId], references: [id], onDelete: Cascade)

  equipments Equipment[]
}

model OrderInteraction {
  id        String          @id @default(uuid()) @map("_id") @db.ObjectId
  orderId   String          @db.ObjectId
  senderId  String?         @db.ObjectId
  type      InteractionType
  content   String?
  fileUrl   String?
  createdAt DateTime        @default(now())
  updatedAt DateTime        @updatedAt

  order  Order @relation(fields: [orderId], references: [id], onDelete: Cascade)
  sender User? @relation(fields: [senderId], references: [id], onDelete: SetNull)
}

enum InteractionType {
  Message
  File
}
```
