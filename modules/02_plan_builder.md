Change Log (2025-11-21): Refined Module 02 based on AI critique: to improved consistency and error handling in the module



### **Module 2 — Plan Builder (Options → Days)**


Purpose
Transform a list of candidate activities into structured daily itineraries that balance proximity, diversity, feasibility, and user constraints.

Inputs
Lodging information: coordinates, check-in/out times

Activity schema (each activity must include):

Name and ID

Type (e.g., attraction, restaurant, park, event)

Tags (indoor/outdoor, family-friendly, accessibility, dietary relevance)

Estimated duration (min–max)

Cost range (low–high)

Coordinates

Opening hours by day

Reservation/ticket requirements

Weather sensitivity

User constraints:

Daily start/end windows

Daily and trip budget caps

Interests and theme priorities

Pace (relaxed, standard, packed)

Dietary needs and accessibility flags

Preferred transit mode

Selection Rules
Time windows (configurable defaults):

Morning: 08:00–11:00

Midday: 11:00–14:00

Afternoon: 14:00–18:00

Evening: 18:00–22:00

Distance thresholds:

Morning activity: ≤ 1.5 km from lodging

Midday activity: ≤ 1.0 km from morning activity or ≤ 20 min transit

Afternoon activity: flexible, but must remain feasible with transit buffers

Evening activity: within reasonable return distance to lodging

Theme diversity:

Avoid repeating the same primary type within a single day

Balance indoor/outdoor activities across the day

Feasibility checks:

Opening hours overlap with planned slot

Duration + travel buffers fit within time window

Cost range aligns with remaining budget

Reservation/ticket requirements satisfied

Loop Structure
For each day:

Morning activity (near lodging)

Filter by proximity and hours

Prefer indoor if poor weather

Score by user interests

Midday activity (close by)

Select near the morning activity

Ensure lunch-compatible options if desired

Afternoon activity (different theme)

Enforce distinct type from earlier slots

Add buffer after lunch

Weather-aware swap if needed

Evening restaurant or optional event

Prioritize events if available and within budget

Otherwise select restaurant matching dietary needs

Flag reservation/ticket requirements

Fallback logic

If no candidate fits, expand radius, relax theme rules, or switch indoor/outdoor

If still failing, mark slot as “free time” with nearby suggestions

Budget and Time Accounting
Track daily budget usage; prefer free/low-cost options when near cap

Include transit time (mode-based) and 15–30 min buffers

Cap total scheduled time per day based on user pace

Output
Each day plan includes:

Morning, Midday, Afternoon, Evening entries with:

Name, type, duration, cost range, distance from prior point

Opening-hours note

Reservation/ticket note

Notes:

Weather contingency

Budget status

Transit mode assumptions

Pseudocode
pseudo
for day in trip_days:
  ctx = {lodging_coord, user_prefs, budget, weather, transit_mode}
  slots = [Morning, Midday, Afternoon, Evening]
  for slot in slots:
    candidates = filter_by_hours(options, day, slot.window)
    ref = lodging_coord if slot == Morning else prior_activity_coord
    candidates = filter_by_distance(candidates, ref, thresholds[slot][transit_mode])
    candidates = apply_preferences(candidates, user_prefs)
    if slot == Afternoon: candidates = enforce_diversity(candidates, day_plan)
    if slot == Evening: candidates = prefer_event_or_restaurant(candidates, user_prefs)
    candidates = check_budget(candidates, budget)
    candidates = ensure_time_fit(candidates, slot.window, transit_mode, buffers)
    pick = score_and_select(candidates)
    if not pick: pick = fallback_strategy(options, ctx, slot, day_plan)
    day_plan.add(slot, pick)
    update_budget_and_time(pick)
output day_plan with notes