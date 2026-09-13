# FlyNow

FlyNow is a self-service airline experience prototype for booking, managing, and getting help with a trip. It is designed as a focused product case study rather than a production airline reservation system.

## The User Problem

Airline customers often have to move between separate booking, manage-booking, check-in, flight-status, baggage, and support experiences. When a trip changes, the customer has to find the right workflow quickly while worrying about fees, availability, refunds, and whether the action actually completed.

FlyNow explores one simple promise: put the most common trip tasks in one calm, predictable self-service flow.

## Target User

The primary user is a mobile-first airline customer who already has, or is about to make, a short-haul booking and needs to complete a task quickly:

- Book a flight with a clear date and time choice.
- Recover from a payment failure.
- Reschedule or cancel an existing booking.
- Resolve routine support questions without waiting for an agent.

The initial market assumption is a price-sensitive traveller in Nigeria booking routes such as Abuja to Lagos, with support for future regional expansion.

## Product Hypothesis

If customers can access booking, trip management, and routine support from one task-oriented home screen, then more customers will complete their intended task without contacting an agent, and customers who encounter payment or itinerary problems will recover with less friction.

The prototype tests the experience and the measurement plan. It does not claim to validate real airline demand, pricing, payment authorization, or operational feasibility.

## Key Prioritization Decisions

- **Prioritized the highest-frequency trip tasks:** book, manage booking, support, check-in, flight status, and baggage help.
- **Used progressive disclosure:** customers choose a task first, then see only the fields and decisions required for that task.
- **Made risky actions explicit:** cancellation includes a warning, refund timing, and a keep-booking option.
- **Included recovery states:** unavailable dates, unavailable slots, and a simulated payment decline show how the experience behaves when the happy path fails.
- **Kept the MVP frontend-only:** a single static prototype makes the flow easy to test and share before investing in airline integrations.
- **Added event instrumentation early:** the key funnel events are ready for PostHog so the next iteration can be guided by behavior rather than opinion.

## Success Metrics

The first version should establish baselines rather than promise targets. Define the reporting window as one unique user session and segment by flow where useful.

| Metric | Definition | PostHog formula |
| --- | --- | --- |
| Booking completion rate | Users who complete booking after starting the booking flow | `booking_confirmed / booking_flow_started` |
| Payment failure recovery rate | Users who complete payment after at least one declined attempt | `payment_failure_recovered / payment_declined` |
| Support self-service resolution rate | Users who receive a self-service answer or completion without an agent | `support_self_service_completed / support entry users` |
| Cancellation completion rate | Users who confirm cancellation after opening cancellation | `booking_cancelled / cancellation_flow_started` |
| Rescheduling completion rate | Users who confirm a new date and time after starting rescheduling | `booking_rescheduled / reschedule_flow_started` |

### Instrumented Events

- `booking_flow_started`
- `flight_search_completed`
- `payment_flow_started`
- `payment_attempted`
- `payment_declined`
- `payment_failure_recovered`
- `payment_succeeded`
- `booking_confirmed`
- `support_flow_started`
- `support_self_service_completed`
- `cancellation_flow_started`
- `booking_cancelled`
- `reschedule_flow_started`
- `booking_rescheduled`
- `booking_abandoned`

## What Is Deliberately Outside This MVP

- Real airline inventory, schedules, fare rules, and seat maps.
- Real payment processing, PCI-compliant card handling, and refunds.
- Authentication, persistent booking records, and account history.
- Email delivery, push notifications, and live chat agents.
- Real-time flight operations data and baggage tracking.
- Multi-city, round-trip, loyalty, accessibility, and disruption-rebooking workflows.
- Production security, privacy consent management, localization, and accessibility certification.

These are intentionally deferred so the prototype can answer the narrower question: does a unified, task-first self-service experience make the core trip jobs easier to understand and complete?

## PostHog Setup

1. Create a PostHog project and copy its **Project API key**.
2. In `flynow-flow.html`, replace `REPLACE_WITH_POSTHOG_PROJECT_API_KEY` with that key.
3. Push to GitHub so Netlify redeploys the site.
4. Open the public site once, complete the booking and support flows, then check PostHog **Activity** for incoming events.
5. Create funnels using the event sequences below:

### Recommended Funnels

- **Booking:** `booking_flow_started` -> `flight_search_completed` -> `payment_succeeded` -> `booking_confirmed`
- **Payment recovery:** `payment_declined` -> `payment_failure_recovered` -> `booking_confirmed`
- **Support:** `support_flow_started` -> `support_self_service_completed`, broken down by `channel`
- **Cancellation:** `cancellation_flow_started` -> `booking_cancelled`
- **Rescheduling:** `reschedule_flow_started` -> `booking_rescheduled`

Create a separate **trend** for `payment_attempted`, `payment_declined`, `booking_abandoned`, and `chat_question_asked` to monitor friction and demand.

The current build uses demo data and simulated outcomes. Treat the first PostHog results as usability evidence for the prototype, not as live commercial performance.