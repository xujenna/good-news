# iOS Walking Tour App — Feasibility Assessment

## TL;DR

**Yes, this is very feasible as a native iOS app.** The core concept — use device location to find nearby Wikipedia articles about historical landmarks and present them as a walking tour — maps cleanly onto iOS frameworks. The main web app logic can be replicated or improved with native APIs.

---

## What the web app likely does

Based on the description, `wiki-tour` probably:

1. Gets the user's coordinates via browser Geolocation API
2. Queries the [Wikipedia Geosearch API](https://en.wikipedia.org/w/api.php?action=query&list=geosearch) for nearby articles within a radius
3. Fetches summaries/extracts for each article (Wikipedia REST API or action API)
4. Filters or ranks results to surface historical/landmark content
5. Presents stops as a list or map-based walking tour with descriptions

---

## iOS Feasibility Breakdown

### Location
- **`CoreLocation`** handles GPS with better accuracy and battery efficiency than browser APIs
- Background location updates (if needed for turn-by-turn style) are built-in
- Easy `CLLocationManager` setup, ~10 lines of code

### Wikipedia API
- The Wikipedia APIs are open, unauthenticated REST endpoints — identical calls work from iOS
- No web scraping or auth tokens needed
- Key endpoints:
  - Geosearch: `https://en.wikipedia.org/w/api.php?action=query&list=geosearch&gsradius=500&gscoord={lat}|{lon}`
  - Page summary: `https://en.wikipedia.org/api/rest_v1/page/summary/{title}`
  - Both return clean JSON

### Map Display
- **MapKit** provides a native map with annotation pins for each landmark — much better UX than a web-based map
- Polyline routing between stops via MapKit or Apple Maps directions
- Satellite/standard map modes built in

### Tour Generation Logic
- Sort landmarks by distance and walking order (nearest-neighbor or straight distance sort)
- Filter by category (check if Wikipedia article categories include "landmark", "historic", etc.)
- This logic is straightforward Swift and does not require a backend

### UI
- `UITableView` or `SwiftUI List` for tour stop list
- `MKMapView` or SwiftUI `Map` for map view
- `WKWebView` or `SFSafariViewController` for full Wikipedia article view
- Native feels significantly better than a web wrapper for location-based apps

### Offline / Caching
- Cache Wikipedia summaries with `URLCache` or Core Data for offline viewing mid-tour

---

## Suggested Tech Stack

| Concern | Web App | iOS Equivalent |
|---|---|---|
| Location | Browser Geolocation API | CoreLocation / CLLocationManager |
| Wikipedia data | Fetch API + Wikipedia REST | URLSession + Wikipedia REST (same API) |
| Map display | Leaflet.js / Google Maps JS | MapKit (native, free) |
| Tour ordering | JS array sort | Swift sort/filter |
| Article view | `<iframe>` or fetch+render | SFSafariViewController |
| Persistence | localStorage | UserDefaults / Core Data |

---

## Scope Estimate

A basic working iOS app (location → nearby landmarks → map + list view → article detail) is a **1–2 week** build for an experienced iOS dev, or a good 2–3 week project for someone learning SwiftUI.

A polished v1 with turn-by-turn walking order, offline caching, and a nice tour UI is more like **3–4 weeks**.

---

## Risks / Considerations

- **Wikipedia API rate limits**: Unauthenticated requests are allowed but heavy usage should add a `User-Agent` header per Wikipedia's [API etiquette](https://www.mediawiki.org/wiki/API:Etiquette)
- **Location accuracy**: Historical landmarks are fixed points; GPS drift is rarely a problem, but accuracy radius should be shown to users
- **Content quality**: Wikipedia geosearch returns everything nearby, not just landmarks — some filtering by article category or page views helps surface quality results
- **App Store review**: Location-based apps need a clear privacy justification in the `NSLocationWhenInUseUsageDescription` key

---

## Recommendation

Port the web app to **SwiftUI + MapKit + URLSession**. The Wikipedia API is the same, location handling is better on native, and MapKit gives a much higher-quality map experience with zero cost. This is a great candidate for a native iOS app.
