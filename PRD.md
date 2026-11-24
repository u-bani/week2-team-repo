# Product Requirements Document (PRD): Campus Shuttle Queue & Capacity Dispatch System

**Course:** BU-COS 209 Backend Engineering (Week 4)  
**Group:** Group 7  
**Team Members:** Ubani Uchechukwu (Leader), Yakubu John Kyomnom, Ozioko Paschal, Tuoyo Omatsuli, Sogbesan Moshood, Ozioko Gerald  
**Date:** 25th November, 2026  

---

## 1. Overview

Getting a shuttle on campus between 7:30 AM and 9:30 AM—or right after afternoon lectures end—is a daily gamble. Students stand at bus stops with no idea whether a shuttle is coming, how full it already is, or if they would be better off just walking. Meanwhile, drivers and park marshals manage turns manually, leading to arguments at the park and bunching along the road.

We spent 2 days observing Main Gate Park and talking to 64 students and 5 shuttle operators. On average, students lose 26 minutes per peak trip waiting without information, and 72% said shuttle delays have made them late to a class or test this semester. This PRD outlines the first version (MVP) of a **Campus Shuttle Queue & Capacity Dispatch System**—a lightweight backend coordination tool that tracks live stop congestion, keeps driver turns fair, and estimates wait times without needing GPS hardware installed on every bus or locking us into a programming language too early.

---

## 2. What We Observed on Campus

### 2.1 The Real Problem
During morning rush (`7:30 AM – 9:30 AM`) and afternoon dispersal (`3:00 PM – 5:30 PM`), crowds pile up fast at Main Gate Park, Hostel Complex Stop, and Faculty of Science Stop. Watching the flow on ground, three things break down every single day:

1. **Students are flying blind:** If you are standing at an intermediate stop, you cannot tell if the next bus coming down the road already left the main park full, or if three buses just broke down or went off-duty.
2. **Queues collapse when crowds build:** Without clear dispatch order or crowd visibility, a sudden wave of students leaving a lecture hall turns a neat line into pushing and queue-jumping.
3. **Drivers bunch up and argue over turns:** Drivers sit at the park waiting to fill every last seat, which causes three shuttles to leave back-to-back followed by a 25-minute gap with nothing. When rush hour hits, disagreements break out over whose turn it is to load next.

### 2.2 Who Deals With This Every Day

| Group | Who They Are | What Frustrates Them Most |
| :--- | :--- | :--- |
| **Student Commuters** | Undergraduates and postgrads moving between hostels, the main gate, and lecture halls 4–5 days a week. | Standing in line for 30 minutes only to give up and trek anyway; missing attendance or morning tests because they misjudged the queue. |
| **Shuttle Drivers** | Drivers running fixed campus routes throughout the day. | Disputes over loading turns at the terminal; sitting idle on a quiet route while another stop is overflowing with passengers. |
| **Park Marshals** | Transport staff or union officials managing loading at major stops. | Trying to control crowded queues with zero visibility into how many students are waiting further down the road. |

### 2.3 How People Cope Right Now
* **"Walk and See":** Students walk all the way to the park just to check the line. If it is too long, they either get stuck waiting or turn around and trek under the sun.
* **WhatsApp Group Pings:** Students drop messages like *"Abeg how main gate be right now?"* in class group chats. By the time someone replies five minutes later, two lecture halls have dismissed and the answer is already wrong.
* **Notebooks & Shouting:** Park marshals write plate numbers in a torn exercise book or keep turns in their heads, calling drivers forward verbally.

### 2.4 Why Current Workarounds Don't Work
* **Information expires too fast:** A crowd at a campus stop can double in three minutes. Informal chat updates cannot keep up.
* **No bird's-eye view:** A marshal at Gate A has no idea that 80 students are stranded at the Hostel stop, so idle buses sit at Gate A instead of moving to where the demand is.
* **Paper logs invite arguments:** Handwritten turn lists are easy to misread or dispute when drivers are rushing to make trips.

---

## 3. Research & Survey Findings

### 3.1 How We Gathered Data
* **On-Site Observation:** Two 45-minute peak sessions at Main Gate Park — Tuesday 8:15 AM–9:00 AM and Wednesday 4:00 PM–4:45 PM.
* **Student Survey:** 64 responses collected across 4 faculties and 100–400 levels.
* **Quick Interviews:** 4 conversations with shuttle drivers and 1 park marshal on duty.

### 3.2 What We Counted at the Park
* **Peak Crowd:** Around 110 students queued up at Main Gate Park around 8:25 AM.
* **Bus Flow:** 7 shuttles departed within a 20-minute window, spending an average of 6 minutes loading and settling change before moving.
* **Key Bottleneck Seen:** Four full buses passed Faculty of Science Stop without stopping while 46 students waited, even though 3 empty shuttles were parked idle at the main terminal.

### 3.3 Survey Numbers
* **81%** of students take campus shuttles at least 4 days a week.
* **63%** wait **more than 20 minutes** during peak lecture hours.
* **72%** have arrived late to a class, practical, or test this semester because of shuttle delays.
* **78%** said not knowing *when* a bus is coming or *if* it has empty seats is worse than the fare or the heat.

### 3.4 What People Actually Said
* **Students don't mind walking—they hate wasting 20 minutes before walking:**  
  > *"If I know the line is going to take 30 minutes, I will just trek from the start instead of standing there for 20 minutes and still trekking late."* — 300-Level Computer Science Student
* **Drivers want fair turns and less guesswork on busy routes:**  
  > *"Sometimes you stay on one side waiting for passengers while plenty people are stranded at another stop, and everybody still argues over whose turn it is to load."* — Campus Shuttle Driver

---

## 4. Proposed Solution & MVP Scope

### 4.1 How Our Solution Works (Conceptually)
Buying and maintaining GPS trackers for every campus shuttle is expensive and unrealistic for a first rollout. Instead, our system tracks shuttle movement and crowd levels using **checkpoint check-ins and student crowd reports**:

1. **Simple Terminal & Stop Check-Ins:** When a driver enters a park, they (or the park marshal) tap once to join the digital turn queue. When the bus leaves, they log how many empty seats are left (`0` if full).
2. **Crowd-Verified Stop Status:** Students waiting at a stop can tap to report whether the crowd is `LOW`, `MODERATE`, `HIGH`, or `CRITICAL`. The backend groups recent reports from verified campus users so one person cannot fake a crowd.
3. **Fair FIFO Turn Queue:** Drivers at a terminal are lined up strictly First-In, First-Out based on check-in time, removing guesswork and turn arguments.
4. **Realistic Wait-Time Estimates:** The backend checks how many active shuttles are on a route, how many seats they still have, and how crowded a stop is to give students a practical wait-time estimate before they leave their hostel or faculty.

### 4.2 What Is Included in Version 1 (MVP)
1. **Routes & Stops Setup:** Official list of campus routes, stop order, vehicle seat capacities, and average travel time between stops.
2. **Shuttle Status & Seat Tracking:** Live status for every active shuttle (`AT_TERMINAL`, `LOADING`, `IN_TRANSIT`, `OFF_DUTY`) plus remaining seat count updated at departure.
3. **Digital Driver Turn Queue:** Automatic FIFO queue at terminals showing each driver's exact position in line.
4. **Live Stop Crowd & Wait-Time View:** A simple lookup where students pick their stop and see the crowd level, active buses on that route, and estimated wait time.
5. **Reallocation Alerts for Marshals:** An automatic alert when one stop hits `CRITICAL` crowd levels while another park has 3 or more shuttles sitting idle.

### 4.3 What We Are Leaving Out of Version 1 (Out of Scope)
* **In-app fare payments or wallets:** Handling student money adds regulatory and dispute headaches we do not need in v1. Fares stay as they are today.
* **Hardware GPS devices on buses:** Too costly to install and maintain; checkpoint updates give us enough accuracy without hardware.
* **Seat pre-booking:** Campus shuttles are short 5–10 minute hops; holding seats for absent riders would slow boarding down.

---

## 5. User Stories & Acceptance Criteria

### For Students
* **US-01: Check stop crowd and wait time before walking out**  
  *As a student commuter, I want to see how crowded my stop is and roughly how long I will wait, so I can decide whether to catch a shuttle or start walking.*
  * **Done when:**
    * Given a valid route and stop, when a student checks the status, the system returns the crowd level (`LOW`, `MODERATE`, `HIGH`, `CRITICAL`), number of active shuttles, and an estimated wait range within 2 seconds.
    * If no shuttles are active on that route (e.g., late at night), the system clearly states `"No Active Shuttles"` instead of showing a fake countdown.

* **US-02: Report crowd size at a stop**  
  *As a student standing at a stop, I want to report how long the queue is, so other students and marshals know what is happening.*
  * **Done when:**
    * Given a verified student account, when they submit a crowd level, the system saves it with a timestamp and updates the stop's live crowd status.
    * If the same student tries to submit another report for the same stop within 15 minutes, the system blocks the duplicate to prevent spam.

### For Drivers & Park Marshals
* **US-03: Join the terminal queue fairly**  
  *As a shuttle driver arriving at a park, I want to check in and see my exact turn number, so nobody jumps ahead of me.*
  * **Done when:**
    * Given a driver finishing a trip or starting a shift, when they check into a terminal, they are placed at the back of that terminal's FIFO queue.
    * Only the shuttle at `Position 1` can move to `LOADING`, unless a Park Marshal logs a manual override with a reason.

* **US-04: Log departure and empty seats**  
  *As a park marshal (or driver), I want to mark a shuttle as departed and record any empty seats, so downstream stops know whether to expect space.*
  * **Done when:**
    * Given a shuttle in `LOADING` state, when departure is logged with `remaining_seats = 0`, its status changes to `IN_TRANSIT`, the next bus in line moves to `Position 1`, and intermediate stops see the incoming bus as `FULL`.

* **US-05: Spot overcrowded stops early**  
  *As a park marshal, I want an alert when a stop is overflowing while another park has idle shuttles, so I can send relief buses quickly.*
  * **Done when:**
    * Given Stop A stays at `CRITICAL` for over 5 minutes while Terminal B has at least 3 shuttles waiting `AT_TERMINAL` for over 10 minutes, the system flags a reallocation alert for the marshal.

---

## 6. Core Data & Backend Logic
*(Written without tying the design to any specific programming language or database)*

### 6.1 Main Data Entities

* **User:** `user_id`, `full_name`, `role` (`COMMUTER`, `DRIVER`, `MARSHAL`, `ADMIN`), `campus_id` (matric number or staff/driver ID), `status` (`ACTIVE`, `SUSPENDED`).
* **Route & Stop:** `route_id`, `route_name`, `stop_id`, `stop_name`, `stop_order` (1, 2, 3...), `avg_minutes_from_previous_stop`.
* **Shuttle:** `shuttle_id`, `plate_number`, `driver_id`, `seat_capacity`, `available_seats`, `state` (`OFF_DUTY`, `AT_TERMINAL`, `LOADING`, `IN_TRANSIT`), `current_route_id`, `last_stop_id`, `last_updated_at`.
* **DispatchQueueEntry:** `entry_id`, `terminal_stop_id`, `shuttle_id`, `checked_in_at` (controls FIFO order), `queue_state` (`WAITING`, `LOADING`, `DISPATCHED`, `CANCELLED`), `override_note` (required if a marshal skips the queue).
* **CrowdReport:** `report_id`, `stop_id`, `reporter_id`, `crowd_level` (`LOW`, `MODERATE`, `HIGH`, `CRITICAL`), `created_at`, `expires_at` (`created_at + 20 mins`).

### 6.2 How the Backend Processes Events

#### 1. Shuttle State Rules
A shuttle cannot jump randomly between states; the backend enforces this cycle:
* `OFF_DUTY` $\rightarrow$ `AT_TERMINAL` (Driver starts work and checks into a park)
* `AT_TERMINAL` $\rightarrow$ `LOADING` (Driver reaches Position 1 and starts boarding)
* `LOADING` $\rightarrow$ `IN_TRANSIT` (Bus leaves park; remaining seats and departure time are saved)
* `IN_TRANSIT` $\rightarrow$ `AT_TERMINAL` (Bus finishes the route and joins the destination queue)
* Any active state $\rightarrow$ `OFF_DUTY` (Driver closes for the day or reports a fault; bus is immediately removed from active supply)

#### 2. Estimating Wait Time at Any Stop
When a student checks `Stop S` on `Route R`:
1. **Check Current Demand:** Pull all active (unexpired) `CrowdReport` records for `Stop S`, giving more weight to reports from the last 10 minutes, to estimate how many students are waiting.
2. **Check Incoming Seats:** Find all `IN_TRANSIT` shuttles upstream of `Stop S` that still have `available_seats > 0`, plus whichever shuttle is currently `LOADING` at the origin terminal.
3. **Calculate Wait Range:**
   * If incoming empty seats can cover the waiting crowd, the wait time is simply the travel time from the nearest non-full shuttle's `last_stop_id` to `Stop S`.
   * If the crowd is larger than the incoming seats, the backend adds the turnaround wait time for the next shuttles queued `AT_TERMINAL`.

---

## 7. Practical Constraints & Quality Requirements

* **Works on Weak Campus Network:** Mobile data slows to a crawl when hundreds of students gather around lecture theatres. Responses must stay small (under 15 KB of plain data) so status checks load reliably on shaky 3G/4G connections.
* **Old Data Expires Automatically:** Crowd reports expire after 20 minutes. If a shuttle has not logged an update in 45 minutes, the backend marks it `STALE` and removes it from wait-time math so students aren't misled by "ghost" buses.
* **Strict Role Permissions:** Students can view wait times and submit rate-limited crowd reports, but only verified Drivers and Marshals can update shuttle states or terminal queues.
* **Transparent Overrides:** If a Park Marshal lets a driver skip the FIFO queue (for example, to handle an emergency rush), the system logs who did it, when, and why.

---

## 8. How We Will Know It Works (Success Metrics)

| Goal | Target (First 4 Weeks) | How We Measure It |
| :--- | :--- | :--- |
| **Shorter Waits** | Cut average peak wait time at main stops by **25% or more** (from 26 mins down to 19 mins or less). | Compare our 26-minute baseline survey average against post-launch trip logs and a follow-up student poll. |
| **Accurate Estimates** | **80%+** of wait-time estimates are accurate within $\pm 5$ minutes. | Compare logged wait-time predictions against actual shuttle departure timestamps. |
| **Fair Dispatch** | **95%+** of terminal departures follow the FIFO queue order without unlogged skips. | Audit `DispatchQueueEntry` logs against marshal override records. |
| **Real Campus Use** | **300+ weekly active students** and **80%+ of active drivers** checking in daily. | Count unique verified student lookups and daily driver check-ins. |

---

## 9. Risks & How We Handle Them

| What Could Go Wrong | Risk Level | How We Prevent or Fix It |
| :--- | :--- | :--- |
| **Drivers forget to tap when rushing during peak hours** | High | Let a single Park Marshal at the main terminal log departures for all buses leaving that park, so drivers don't have to touch their phones while driving. |
| **Students spam `CRITICAL` reports just to pull buses to their stop** | Medium | Require campus ID login, enforce a 15-minute cooldown per user, and require at least 3 separate student reports within 10 minutes before flagging a stop as `CRITICAL`. |
| **Drivers resist digital turn tracking** | Medium | Involve park union leaders early and show how timestamped FIFO logs protect drivers from unfair turn-skipping. |