# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Smith Mountain Lake Directory is a single-page web application that displays local businesses around Smith Mountain Lake, Virginia. The application is a self-contained HTML file with no build process.

## Architecture

### Tech Stack
- **Frontend**: Vanilla JavaScript (no framework)
- **Database**: Supabase (PostgreSQL)
- **External APIs**:
  - Google Maps JavaScript API (Places Library, Maps Library)
  - Supabase client library (v2)

### Single-File Architecture
The entire application lives in `index.html` with:
- Inline CSS styles
- Inline JavaScript
- CDN-loaded dependencies (Supabase client, Google Maps)

### Data Flow
1. **Import**: Google Places API → JavaScript → Supabase `businesses` table
2. **Display**: Supabase `businesses` table → JavaScript → DOM (List or Map view)

### Database Schema
The Supabase `businesses` table stores:
- `id` (auto-generated)
- `name` (string)
- `category` (string: Restaurant, Lodging, Cafe, Store, Marina, Other)
- `address` (string)
- `lat` (float)
- `lng` (float)
- `rating` (string)
- `phone` (string)
- `description` (string - from Google Places editorialSummary)
- `imageUrl` (string - from Google Places photos)

### Key Functions
- `loadBusinessesFromDB()`: Fetches all businesses from Supabase on page load
- `saveBusinessesToDB()`: Bulk inserts new businesses to Supabase
- `importFromGoogle()`: Fetches businesses from Google Places API using text queries
- `renderBusinesses()`: Renders business cards in list view with filtering
- `initMap()`: Initializes Google Map with markers for each business
- `switchView()`: Toggles between list and map views

## Development Workflow

### Testing
Open `index.html` directly in a browser. No local server required (though CORS may require one for some features).

The `test.html` file is a minimal test page for validating Google Places API functionality.

### Version Management
Update the version number in the footer (currently "Version 4.3") when making significant changes:
```html
<div style="text-align:center; padding:20px; color:white; font-size:12px;">
    Version X.X - Description
</div>
```

### API Credentials
- **Supabase**: Credentials are hardcoded in lines 229-231 (public anon key)
- **Google Maps**: API key is hardcoded in line 235

### Data Import Process
The import function searches for 5 different business categories:
1. Restaurants
2. Hotels
3. Cafes
4. Shops
5. Marinas

Each search returns up to 20 results. The app includes a 300ms delay between requests to avoid rate limiting.

### Google Places API Fields
When adding new data fields, update the `fields` array in the request (line 327):
```javascript
fields: ['displayName', 'formattedAddress', 'location', 'rating', 'types', 'nationalPhoneNumber', 'editorialSummary', 'photos']
```

Then update the data structure in the `allPlaces.push()` call (lines 344-353).

## Important Notes

### Clear Data Before Re-importing
After schema changes, users must:
1. Click "Clear All Data" button
2. Click "Import from Google Places" button

This ensures the Supabase table matches the new schema.

### Photo URLs
Google Places photos use the `.getURI()` method with size parameters:
```javascript
place.photos[0].getURI({maxWidth: 400, maxHeight: 300})
```

### Search and Filtering
The app filters businesses by:
- Search term (matches name or address)
- Category dropdown (populated dynamically from loaded businesses)

Both list and map views respect these filters.
