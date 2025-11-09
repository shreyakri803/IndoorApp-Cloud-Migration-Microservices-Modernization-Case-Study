# Test Strategy & UAT

## Scope
- Smoke + UAT on key journeys: Book, Cancel, Dashboard.

## Approach
- Checklist runs with screenshots (local UI/CLI).  
- DR tabletop: simulate API failure; execute rollback.

## UAT Cases
- **U1:** Book a room → returns Booking ID.  
- **U2:** Cancel booking → status = Cancelled.  
- **U3:** Dashboard loads → 200 OK.

## Exit Criteria
- All UAT pass, no Sev-1 open; rollback verified.
