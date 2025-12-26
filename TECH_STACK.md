# Technical Stack & Architecture Documentation

## Overview
**Gastos Tracker** is a voice-enabled personal expense tracking web application designed for Spanish-speaking users (Argentina locale). The application runs entirely client-side with no backend dependencies.

**Version:** v1.2.0
**Primary Language:** Spanish (es-AR)
**Target Platform:** Mobile browsers (iOS Safari, Chrome, Edge)

---

## Tech Stack

### Frontend
- **HTML5** - Semantic markup with mobile-first responsive design
- **CSS3** - Custom styling with:
  - System fonts (-apple-system, BlinkMacSystemFont, Segoe UI)
  - Material Design-inspired UI components
  - CSS animations (pulse effect for recording state)
  - Flexbox layout system
  - Sticky positioning for tabs
- **Vanilla JavaScript (ES6+)** - No frameworks or build tools
  - Modern async/await patterns
  - Promise-based data handling
  - Event-driven architecture

### APIs & Browser Features
- **Web Speech API** (SpeechRecognition/webkitSpeechRecognition)
  - Continuous speech recognition
  - Spanish (Argentina) language model (es-AR)
  - 20-second recording timeout
- **IndexedDB API** - Client-side NoSQL database
  - Version 1 schema
  - Two object stores: `expenses`, `settings`
  - Auto-incrementing keys for expenses
- **Blob API** - CSV file generation and downloads
- **Internationalization API** - Number and date formatting for es-AR locale

### Data Persistence
- **IndexedDB** (ExpensesDB v1)
  - Local-only storage (no cloud sync)
  - Persistent across sessions
  - Object stores:
    - `expenses`: Transaction records with auto-increment IDs
    - `settings`: User preferences (payment methods, categories, mappings)

---

## Architectural Decisions

### 1. **Single-File Architecture**
**Decision:** Monolithic single HTML file with embedded CSS and JavaScript

**Rationale:**
- Simplified deployment (just serve one file)
- No build process required
- Reduces HTTP requests to one
- Easy to host on any static server or open locally
- Perfect for mobile PWA distribution

**Trade-offs:**
- Harder to maintain as complexity grows
- No code splitting or lazy loading
- All assets loaded upfront

### 2. **Client-Side Only (No Backend)**
**Decision:** All processing, storage, and logic runs in the browser

**Rationale:**
- Privacy-first: No data leaves the device
- Zero server costs
- Works offline after initial load
- No authentication/authorization complexity
- Instant response times

**Trade-offs:**
- No cross-device sync
- Limited to browser storage quotas
- Data loss if browser storage is cleared
- No server-side analytics

### 3. **IndexedDB for Storage**
**Decision:** Use IndexedDB instead of localStorage or cloud storage

**Rationale:**
- Larger storage capacity (no 5-10MB limit)
- Structured data with indexes
- Asynchronous API (non-blocking)
- Better for storing multiple expenses with relationships
- Native browser support

**Trade-offs:**
- More complex API than localStorage
- No cross-browser data portability
- Requires Promise handling

### 4. **Voice-First Interface**
**Decision:** Primary input method is voice recognition

**Rationale:**
- Hands-free expense logging
- Faster than manual typing on mobile
- Natural language processing for Spanish
- Reduces friction for quick entries
- Mobile-optimized UX

**Trade-offs:**
- Requires microphone permissions
- Internet connection needed for speech recognition (in some browsers)
- Accuracy depends on pronunciation and background noise
- Fallback needed for non-supported browsers

### 5. **Mobile-First Design**
**Decision:** Optimized for mobile screens with touch interactions

**Rationale:**
- Target audience uses smartphones primarily
- Expense logging happens on-the-go
- Touch-friendly button sizes (100px mic button)
- Single-column layout for narrow screens
- Max-width container (600px) for larger screens

### 6. **Tab-Based Navigation**
**Decision:** Single-page app with three tabs: Grabar, Historial, Ajustes

**Rationale:**
- Reduces cognitive load (clear separation of concerns)
- No page reloads (instant switching)
- Maintains app state across navigation
- Familiar pattern for mobile users
- Sticky tab bar for easy access

### 7. **Smart Parsing Logic**
**Decision:** Automatic extraction of amount, payment method, and category from voice input

**Rationale:**
- Reduces manual data entry
- Learns from user's keyword mappings
- Supports Spanish decimal formats ("5000 con 50" = 5000.50)
- Handles both comma and period as decimal separators
- Normalizes whitespace for matching

**Algorithm:**
- First extracts numeric amount (with special "con" pattern support)
- Matches payment method from user's configured list (case-insensitive, whitespace-normalized)
- Matches category either directly or via keyword mappings
- Falls back to "Varios" category if no match found

---

## Application Features

### Core Features

#### 1. **Voice Expense Recording**
- **Speech Recognition**: Tap microphone button to start recording
- **Real-time Transcription**: Displays recognized text
- **Automatic Parsing**: Extracts structured data:
  - **Amount**: Numeric value with decimal support
  - **Payment Method**: Matched against user's configured methods
  - **Category**: Auto-detected via keywords or direct category names
  - **Original Text**: Preserved for reference
- **Visual Feedback**: Pulsing red button during recording
- **Error Handling**: Specific messages for permission denied, no speech, network errors

#### 2. **Expense History**
- **Chronological List**: Shows all expenses sorted by date (newest first)
- **Formatted Display**:
  - Amount in ARS with 2 decimal places (es-AR formatting)
  - Date/time in local format (DD/MM/YYYY HH:MM)
  - Category, payment method, and original text
- **Empty State**: Friendly message when no expenses exist

#### 3. **CSV Export**
- **One-Click Export**: Download all expenses as CSV file
- **Filename**: Auto-generated with current date (`gastos_YYYY-MM-DD.csv`)
- **Encoding**: UTF-8 with proper escaping
- **Headers**: Date, Amount, Category, Payment Method, Text
- **Compatibility**: Works with Excel, Google Sheets

#### 4. **Customizable Settings**

##### Payment Methods
- Add/remove custom payment methods
- Default methods: Transferencia, VisaBBVA, MasterBBVA, Debito, Efectivo
- Case-insensitive matching with whitespace normalization

##### Categories
- Add/remove custom categories
- Default categories: Comida, Extra, Mascotas, Salud, Servicios, Supermercado, Transporte, Varios
- Directly mentioned in voice input for instant categorization

##### Keyword Mappings
- Map trigger words to categories (e.g., "carne" → "Supermercado")
- Default mappings:
  - Food items → Supermercado
  - Transport terms → Transporte
  - Utilities → Servicios
- Enables smart auto-categorization

### User Experience Features

#### Visual Design
- **Color Scheme**:
  - Primary: Blue (#2196F3)
  - Accent: Green for save button (#4CAF50)
  - Warning: Red for amounts and errors (#f44336)
  - Export: Orange (#FF9800)
- **Typography**: System native fonts for optimal rendering
- **Icons**: Emoji-based for universal recognition (🎤, 💰, 📁, 💳, 📝)

#### Interactions
- **Toast Notifications**: Temporary feedback messages (2s duration)
- **Disabled States**: Save button disabled until valid expense detected
- **Keyboard Support**: Enter key submits forms in settings
- **Loading States**: "Escuchando..." during recording

#### Responsive Layout
- **Mobile Optimized**:
  - Full-width buttons
  - Large touch targets
  - Sticky header navigation
- **Desktop Support**: Max-width container (600px) centered on screen

---

## Data Models

### Expense Object
```javascript
{
  id: Number,              // Auto-increment (IndexedDB)
  amount: Number,          // Parsed numeric value
  paymentMethod: String,   // Matched from settings
  category: String,        // Auto-detected or "Varios"
  originalCategory: String, // Raw text before categorization
  rawText: String,         // Full voice transcription
  date: Date               // Timestamp of creation
}
```

### Settings Object
```javascript
{
  key: String,  // "paymentMethods" | "categories" | "mappings"
  value: Any    // Array or Object depending on key
}
```

**Settings Values:**
- `paymentMethods`: `Array<String>` - List of payment method names
- `categories`: `Array<String>` - List of category names
- `mappings`: `Object<String, String>` - Keyword → Category map

---

## Parsing Algorithm Details

### Number Parsing
1. **Check for "con" pattern**: `/([\d.,]+)\s+con\s+(\d+)/i`
   - Example: "5000 con 50" → 5000.50
2. **Extract basic number**: `/([\d.,]+)/`
3. **Determine decimal separator**:
   - Last comma position vs last period position
   - Spanish format: `5.000,50` → 5000.50
   - English format: `5,000.50` → 5000.50

### Payment Method Matching
- Normalize both input and configured methods (lowercase, remove whitespace)
- First match wins
- Default to "Efectivo" if no match

### Category Detection (Priority Order)
1. **Direct category name match**: Check if any word equals a category name
2. **Keyword mapping match**: Check if any word exists in mappings object
3. **Fallback**: "Varios"

### Original Text Extraction
- Remove numeric values and the word "con"
- Join remaining words
- Preserves context for user reference

---

## Browser Compatibility

### Required Features
- **ES6+ JavaScript**: async/await, arrow functions, template literals, destructuring
- **IndexedDB**: Supported in all modern browsers
- **Web Speech API**:
  - ✅ Chrome/Edge (Android/iOS)
  - ✅ Safari (iOS 14.5+)
  - ❌ Firefox (limited support)
- **Blob API**: Universal support

### Tested Platforms
- iOS Safari 14.5+
- Chrome/Edge Android
- Chrome/Edge Desktop (limited value for mobile-first app)

---

## Security & Privacy

### Privacy Features
- **Local-only storage**: No data transmission to external servers
- **No analytics**: Zero tracking or telemetry
- **No cookies**: No session tracking
- **No third-party scripts**: Self-contained application

### Permissions Required
- **Microphone**: Required for voice recognition
- **Storage**: IndexedDB automatically available

### Data Security
- **Client-side only**: Data never leaves the device
- **Browser sandboxing**: Protected by browser security model
- **No authentication**: No passwords or credentials stored

---

## Deployment

### Hosting Requirements
- Any static file server
- No server-side processing needed
- Single file to deploy: `index.html`

### Suggested Hosting Options
1. **GitHub Pages**: Free static hosting
2. **Netlify/Vercel**: Free tier with HTTPS
3. **Local file system**: Open directly in browser (file://)
4. **Apache/Nginx**: Simple static file serving

### PWA Potential
- Can be enhanced with Service Worker for offline support
- Add manifest.json for "Add to Home Screen" functionality
- Already mobile-optimized and installable

---

## Performance Characteristics

### Load Time
- **Single HTTP request**: ~32KB uncompressed HTML
- **No external dependencies**: No CDN requests
- **Instant render**: Inline CSS eliminates FOUC

### Runtime Performance
- **Database operations**: Async, non-blocking
- **Voice recognition**: Offloaded to browser/OS
- **Rendering**: Minimal DOM manipulation

### Storage Usage
- **Settings**: ~1-5 KB
- **Per expense**: ~200-500 bytes
- **1000 expenses**: ~500 KB
- **IndexedDB quota**: Typically 50MB+ per origin

---

## Future Enhancement Opportunities

### Potential Improvements
1. **Offline Support**: Add Service Worker for PWA
2. **Cloud Sync**: Optional Firebase/Supabase integration
3. **Charts/Analytics**: Spending visualizations
4. **Expense Editing**: Modify/delete existing entries
5. **Budgets**: Set category limits and alerts
6. **Multi-currency**: Support USD, EUR, etc.
7. **Receipt Photos**: Attach images via camera API
8. **Recurring Expenses**: Automatic monthly entries
9. **Search/Filter**: Find expenses by category, date, amount
10. **Dark Mode**: Reduce eye strain in low light

### Architectural Considerations for Scaling
- **Code Splitting**: Separate JS/CSS files
- **Framework Migration**: React/Vue if complexity increases
- **TypeScript**: Add type safety
- **Testing**: Unit tests for parsing logic
- **Build Process**: Minification, bundling

---

## Version History

### v1.2.0 (Current)
- Better error handling for microphone permission errors

### v1.1.5
- Fix CSV encoding issues by using English ASCII-only headers

### v1.1.4
- Previous stable release

---

## Development Notes

### Code Organization
- Lines 1-339: HTML structure and CSS styles
- Lines 340-428: HTML body with tab structure
- Lines 429-935: JavaScript application logic

### Key Functions
- `initDB()`: IndexedDB initialization (451-473)
- `parseExpense(text)`: Core parsing logic (634-721)
- `startRecording()`: Voice recognition handler (723-804)
- `saveExpense()`: Persist to IndexedDB (806-821)
- `exportToCSV()`: Generate and download CSV (872-903)

### Event Handlers
- Tab switching: Lines 905-917
- Button clicks: Lines 919-924
- Keyboard shortcuts: Lines 926-932
- App initialization: Line 934
