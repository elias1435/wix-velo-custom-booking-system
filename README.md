# Wix Velo Custom Booking System (Book Now)

A reusable prompt pack + implementation outline for building a fully custom booking calendar in **Wix Velo (Vibe Coding)**.

## Features
- Custom **Book Now** page
- Date + time slot listing
- Slots become **Booked** (disabled) once saved in CMS (prevents double booking)
- Stores bookings in **Wix CMS**
- Booking preview panel (service/date/time/name/email/phone)
- Thank you message after booking + “you will receive an email soon”
- Add to calendar:
  - Google Calendar link
  - Microsoft / Outlook Calendar link
  - Download `.ics` calendar file
- Optional email confirmations via **Wix Automations**

## Screenshots
![Booking UI](https://github.com/elias1435/wix-velo-custom-booking-system/blob/main/wix-booking-1.jpg)
![Thank You + Calendar Buttons](https://github.com/elias1435/wix-velo-custom-booking-system/blob/main/wix-booking-2.webp)

## Data Model (CMS)

### Services (Collection ID: `Services`)
- `name` (text)
- `slotDurationMins` (number)
- `dailyStartTime` (text, e.g. "09:00")
- `dailyEndTime` (text, e.g. "17:00")

### Bookings (Collection ID: `Bookings`)
NOTE: `startDateTime` and `endDateTime` are **TEXT** fields (ISO strings), not Date fields.
- `serviceId` (text)
- `serviceName` (text)
- `startDateTime` (text)  // ISO string
- `endDateTime` (text)    // ISO string
- `slotKey` (text, UNIQUE) // `${serviceId}_${YYYY-MM-DD}_${HH:mm}`
- `customerName` (text)
- `customerEmail` (text)
- `customerPhone` (text)
- `status` (text) default "confirmed"

Permissions (Bookings):
- Create: Anyone
- Read: Admin only
- Update: Admin only
- Delete: Admin only

## Implementation Files (Suggested)
- `backend/booking.jsw`  
  Exports:
  - `getAvailability(serviceId, selectedDate)`
  - `bookSlot({ serviceId, serviceName, slotStartISO, slotEndISO, customerName, customerEmail, customerPhone })`
  - `getCalendarLinks({ title, description, location, startISO, endISO })`

- `pages/BookNow.js` (or Wix page code: "Book Now")  
  - Loads services
  - Renders slots repeater
  - Shows Booked slots for everyone
  - Live preview panel sync
  - Booking submit + thank you box + calendar links

## Important Logic Notes
- Because booking start/end are stored as **TEXT**, availability checking is done using `slotKey` matching.
- Double-booking protection is enforced by:
  1) computing a deterministic `slotKey`
  2) `slotKey` being UNIQUE in the Bookings collection
  3) backend insert failing if already booked

## Prompt Pack
See: `prompts/` folder (copy/paste prompts for Wix Vibe Coding AI).

## Email Confirmation (Optional)
Use **Wix Automations**:
Trigger: When a new item is created in Bookings → Action: Send email to `customerEmail`.

## License
MIT (optional)
