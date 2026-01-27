---
cssclasses:
---
# Cat MineStar Fleet Assignment Engine Logic

The sources provide a deep and consistent look into the operation and assignment functionality of the **Cat MineStar System Fleet** (referred to generally as Fleet, or specifically using terms like **DISPATCH** and **Joptimizer**), positioning it as an integrated, complex system designed to maximize mine productivity and minimize cost through automated, real-time decision-making [1-7].

The core of these operations revolves around the **Assignment Engine Logic**, which transforms continuous data inputs into specific instructions (assignments) for haul trucks and other equipment [1, 3, 8-11].

## I. System Architecture and Operational Goal

The Fleet system relies on several hardware and software components, including application, database, and reporting servers, user workstations (Mine Controller and Builder), and mobile terminals that communicate via a wireless network using **TMAC messages** [5, 12-23].

The overall objective of the assignment process is explicitly defined as **maximizing overall production** while **minimizing each component of the haul cycle**, thereby reducing the overall cost of production (moving more tonnes as cheaply as possible) [1-4].

## II. The Core Assignment Engine Logic

The optimization strategy employs a two-phase mechanism using three primary mathematical programming models—Best Path (BP), Linear Programming (LP), and Dynamic Programming (DP) or Dynamic Assignment (DA)—to achieve optimal truck assignments [1, 10, 11, 24-31].

1. **Best Path (BP) Model:** This module calculates the shortest and quickest available path between two points (locations/nodes), using the mine model (MineGraphics representation) as its foundation [19, 27, 32-37]. The accuracy of the path calculation is highly dependent on the correct elevation of callpoints and the maintenance of a "healthy road network" [34, 37, 38].

2. **Linear Programming (LP) Layer:** The LP layer creates the **theoretical master production plan (LP solution)** [10, 35, 39-45].

• **Function:** LP determines optimized production circuits (loading tool, processor, and material combinations, known as Production Arcs) and the **optimal haulage flow rates** (feed rates) required to maximize overall truck productivity under various constraints [39, 46-51].

• **Key Inputs/Constraints:** LP considers shovel digging rates, production capacities (% coverage), material/dump compatibility, blending requirements, haulage resource availability (trucks), and restrictions (locks/bars) [46, 52-59].

• **Recalculation:** The LP solution is automatically recalculated on a regular **time-based interval** (default 600 seconds/10 minutes, minimum 60 seconds) and also triggered by **event-based triggers**, such as changes to shovel/dump status, grades, locks, or road networks [40, 42, 44, 60-64].

3. **Dynamic Assignment (DA)/Dynamic Programming (DP) Layer:** This layer executes the LP solution in **real-time** in response to immediate assignment requests from trucks, striving for **"balance and synchronization"** while minimizing lost tonnage [1, 6, 10, 11, 28, 65-67].

• **Empty Truck Assignment (to Shovel):** DP first determines the **neediest shovel** (either based on idle time greater than a threshold, or by the maximum difference between Required Haulage and Current Haulage) [68-71]. It then finds the **neediest route** to that shovel [72, 73]. Finally, it selects the available truck whose assignment creates the **least cost** (smallest production loss), weighing travel time (cost factor of 1.5 default) higher than idle time (cost factor of 1.0 default) [74-76].

• **Loaded Truck Assignment (to Dump):** Assignment follows a strict **Dump Lock Hierarchy** [77-80]. The highest priority is a **Truck Dump Lock**, which overrides all other locks [77, 79]. If no locks are applied, the truck is assigned to the **neediest loaded LP Path** that originates from the loading shovel, typically resulting in the closest dump that accepts the material and is Ready [81-85].

III. Types and Triggers of Assignments

Assignments are the instructions directing a truck to a loading tool, processor, or station [1, 8].

|   |   |   |
|---|---|---|
|Type|Mechanism/Trigger|Purpose/Context|
|**Automatic**|System-generated without direct intervention [86]. Triggered by **state changes** (like entering Traveling Empty after dumping), load reports (Truck finishes loading), misroutes, reassignment waypoints, or predetermined schedule checks (e.g., low fuel) [60, 64, 84, 87-93].|**Preferred mode** of operation to optimize production automatically [86].|
|**Scheduled Automatic**|Enacted when a **pre-configured time window** can be met (e.g., refuel assignments targeting a "Required At" time) [86, 94, 95].|Optimizes non-production tasks (like fueling or breaks) to minimize downtime [95, 96].|
|**Manual**|Initiated directly by the Mine Controller (via utilities like Truck Assistant or drag-and-drop) or by the truck operator (pressing an "Assign" button) [87, 88, 97, 98].|Used to override automatic assignments, troubleshoot, or quickly reposition trucks [87, 99-101].|
|**Manual Scheduled**|Deferred assignment enacted when the truck's activity reaches a specified load state or activity (e.g., assigning the next task while the truck is loaded) [86, 102, 103].|Better handles exceptions than immediate manual assignments, minimizing disruption to DP logic [104].|

IV. User Impact and Data Integrity

The accuracy of assignment decisions critically relies on the quality of data entered by **Fleet Users** (Controllers and Builders) [23, 105-109].

• **Consequence of Poor Data:** Without accurate mine knowledge, Fleet sends **poor assignments**, resulting in trucks queuing, idle loaders, misdirected loads, inaccurate production recording, and higher costs per ton [23, 105-107, 109].

• **Data Corruption Risk:** Manual interventions by controllers (e.g., manual assignments, or incorrect manual input of activity states) directly disrupt DP calculations and cycle times, risking data integrity issues such as **zero spot/load times** and inaccurate travel times [104, 108, 110-112].

• **Key Inputs Impacted by Users:** Users must ensure the integrity of the spatial mine model (waypoints, elevations), operational parameters (machine availability, constraints/restrictions/locks), and real-time inputs (correct delay entry, following assignments) [108, 113-115]. Incorrect GPS position data due to poor communication or faulty hardware can lead to misroute detection or incorrect ETA calculations, further affecting assignment quality [116-118].

In short, the success of the Cat MineStar System/DISPATCH is a function of its sophisticated optimization algorithms married to high-quality, continuous, and accurate data inputs, primarily maintained through the diligence of the Mine Controllers and Builders [2, 105, 119].

--------------------------------------------------------------------------------

## Mine Haulage Optimization Logic and Inputs

The **Assignment Engine Logic and Inputs** form the core of the Cat MineStar System Fleet (and Modular Mining's DISPATCH/Leica's Joptimizer solutions), driving automated decisions that aim to maximize production and minimize operating costs by optimizing haul truck assignments [1-4]. The system's effectiveness hinges entirely on the quality and accuracy of the inputs it receives, as inaccurate data leads to suboptimal assignments, queuing, idle equipment, misdirected loads, and higher costs per ton [5-9].

The overall optimization strategy (DOS/Joptimizer) operates in phases, utilizing two main components: the **Linear Programming (LP) Layer** for strategic planning and the **Dynamic Assignment (DA) Layer** for real-time execution [1, 10-16].

I. The Assignment Engine Logic: LP and DA Layers

The Assignment Engine Logic is structured to create a comprehensive production plan and then dynamically execute assignments based on live operational changes:

1. Linear Programming (LP) Layer (The Master Plan)

The LP layer’s purpose is to create a theoretical **master plan (LP solution)** for maximizing overall truck productivity [1, 2, 12, 13, 17-19]. This plan defines optimized **production circuits** (or "production arcs"), specifying which dump locations should feed haulage resources (empty trucks) to which shovels, and the required feed rates at which these resources should flow [12, 17, 20-24].

• **Objective:** By minimizing the required hauling requirements (cost function), LP maximizes the achievable production given the current resources and constraints [1, 2, 23, 25].

• **LP Feed Rate:** The output of LP is the calculated optimum rate (tons/hour) at which assignments should be made along each haulage path [20, 23, 26].

• **LP Recalculation Triggers:** The LP solution is recalculated automatically at a configurable time interval (e.g., every 600 seconds/10 minutes, with a minimum of 60 seconds) [27, 28]. It is also triggered by major **event-based triggers** in the mine, such as a shovel or dump status change, a change in grade, locks, priorities, coverage, material, or road status [29-32].

2. Dynamic Assignment (DA) Layer (Real-Time Execution)

The DA layer (also referred to as Dynamic Programming or DP) is responsible for executing the assignments in real time based on the LP master plan [11, 12, 14-16, 33, 34]. DP builds **Truck and Shovel need lists** and works to achieve **"balance and synchronization"** while focusing on the **least amount of lost tonnage** [34-37].

• **Empty Truck Assignment (Shovel Selection):** DP uses a calculated cost metric to determine the best assignment for an empty truck [38, 39]. This is achieved by finding the **neediest shovel** (either by elapsed idle time or haulage deficit) and then selecting the available truck with the **least assignment cost** [38-41].

• **Cost Calculation:** The cost is calculated by summing forecasted travel time (weighted by `travelCost`) and forecasted queue/idle time (weighted by `idleCost`) [39, 41, 42]. By default, travel time is 1.5texttimesmoreexpensivethanidletime [42].

• **Loaded Truck Assignment (Dump Selection):** The logic is simpler, prioritizing assignments based on the **dump lock hierarchy** (Truck Lock > Shovel Lock/Grade Lock) [43-48]. If no locks exist, the truck is sent to the neediest loaded LP Path, typically the closest dump that accepts the material and is Ready [49-52].

• **Manual Assignment Impact:** Manual assignments disrupt the DP logic flow and cycle time calculations, potentially causing data integrity errors like zero spot or load times, inaccurate travel times, shovel hang time, and queuing [44, 53-56].

II. Critical Inputs to the Assignment Engine

The logic relies on four major categories of inputs, many of which require mandatory user interaction and accurate data entry to prevent the system from making poor decisions [5, 6, 8, 57-59].

1. Configuration Inputs (The Mine Model)

These inputs define the static parameters and restrictions necessary for LP to calculate feasible production circuits [24, 60-62].

• **Mine Model/Road Network:** The physical representation of the mine, including **Road Network Definition** and **Callpoint Elevations** (Z coordinates) [46, 61, 63-65]. Incorrect elevations corrupt the data, leading to improper assignment calculations [24, 65, 66].

• **Compatibility:** Defines which equipment and materials are compatible [59, 61, 67-70]. For instance, a dump must be configured to accept the material being hauled [70-72].

• **Restrictions (Locks and Bars):** Rules that define how trucks are constrained. These include **Truck Restrictions**, **Shovel Restrictions**, **Grade Restrictions**, and **Route Restrictions** [59, 73-78]. Minimizing unnecessary restrictions improves production outcomes [79, 80].

• **Equipment Availability/Assignability:** Equipment (shovels, trucks, processors) must be explicitly marked as **Available for Assignment** or **Assignable** to be included in the LP solution [81-87]. An unavailable machine is removed from the production plan [84].

• **Capacity Settings:** This includes **Dump Capacity** (TPH limit) and **Dump Queue Capacity** (number of trucks allowed to queue), which constrain assignment flows to prevent bottlenecks [61, 88-90].

2. Dynamic and Real-Time Operational Inputs

These inputs continuously flow from field equipment to the application server via TMAC messages, enabling real-time decision-making and assignment triggering [3, 91-93].

• **GPS Position Data:** Provides the truck’s location, speed, and heading, essential for calculating travel times, ETAs, and detecting events like off-course travel (misroutes) [3, 92, 93].

• **Activity State Transitions:** Real-time data signaling changes in the haul cycle, such as when a truck starts traveling empty, finishes dumping, or moves more than the **Departure Distance** from the load point [64, 92, 94-97].

• **Shovel Dig Rate & Truck Payload:** The digging rate is continuously measured (based on load times and tonnage) and incorporated as an LP constraint [61, 71, 88, 98]. Truck payload is used for tonnage tracking [61].

• **Fuel Level:** Monitored to trigger **automatic refueling assignments** when the **Fuel Low Percent** or **Fuel Empty Percent** thresholds are breached [61, 99-101].

3. Calculated Inputs

The system generates complex metrics based on real-time data which are used directly in assignment cost calculation:

• **Average Speeds / Travel Times:** Rolling averages are calculated for individual road segments, factoring in load state, road category, and gradient [59, 75, 102-104]. These dynamic times are crucial for determining the shortest and lowest-cost paths [102, 105].

• **Spot Time / Load Time:** Calculated based on physical movements and sensor inputs, significantly influencing shovel capacity calculations [106-108].

• **TKPH Values:** Calculated to monitor tire health. If the truck exceeds the warning threshold, the assignment logic restricts the truck to routes with the lowest average TKPH values to mitigate tire wear [59, 106, 109-112].

Summary of Data Quality and Control

The quality of these inputs is the **foundation of optimization** [5, 6, 8]. Controllers and Builders are responsible for maintaining data integrity through processes such as regularly checking the spatial mine model, correcting cycle errors, properly setting status and delays, and accurately applying restrictions [80, 113-116]. Without accurate input knowledge, the system cannot make the best assignment decisions [5, 6, 8, 57].

--------------------------------------------------------------------------------

## Mine Fleet Management System Assignment Logic

The sources provide a clear and consistent definition of what an assignment is within the context of mine fleet management systems (DISPATCH, Cat MineStar Fleet, or Joptimizer), emphasizing its function as the primary output of the Assignment Engine Logic, which relies heavily on comprehensive data inputs.

Definition and Purpose of an Assignment

An **assignment** is fundamentally defined as a **set of instructions that tells the truck operator to travel** from its current location to a given destination [1-3].

These instructions direct the truck operator to one of three types of locations to perform a specific activity [3]:

1. A **Loading Tool** (shovel, excavator, or loader) – to load an empty truck [3].

2. A **Processor** (dump, crusher, or stockpile) – to unload a loaded truck [3].

3. A **Station** – to perform other activities, such as fueling, maintenance, or shift change [3, 4].

Assignments are displayed to truck operators on an **on-board display** (Jpanel/field computer) [3, 5].

The central purpose of generating and issuing these assignments is directly tied to the system's core optimization objective: to **maximize overall production** and **minimize the cost of production** (by reducing the duration of non-productive haul cycle components, such as travel and idle time) [6, 7].

The Assignment in the Context of Haul Cycle Logic

Assignments are the result of the system continuously monitoring the haul cycle and reacting to various **Assignment Triggers** using complex algorithms [6, 8].

The entire haul cycle for a productive truck moves sequentially through defined activities, from **Traveling Empty to Dumping** (one complete cycle) [9-11]. An assignment dictates the planned next step in this cycle:

• **Loading Assignment (Empty Truck):** This instruction is generated when a truck finishes dumping and enters the **Traveling Empty state**, prompting the office software to calculate an assignment to a shovel [12-14].

• **Dumping Assignment (Loaded Truck):** This instruction is generated when the loading unit operator signals that loading is complete, directing the truck to the designated processor [15, 16].

Assignment Engine Logic & Inputs

The generation of an assignment is the culmination of the two primary layers of optimization logic working based on continuous data inputs:

1. **Linear Programming (LP) Layer:** This layer establishes the **theoretical master plan** for the mine, calculating optimized production circuits (or "production arcs") that dictate **haulage flow paths and rates** [17-20]. LP determines where assignments _should_ send trucks to maximize productivity based on static configuration data and general constraints [21, 22].

2. **Dynamic Assignment (DP/DA) Layer:** This layer receives the LP master plan and executes assignments in **real time** in response to immediate triggers (like a state change or an assignment request) [18, 23, 24]. The DP/DA process uses current conditions (like traffic, delays, and truck position) to select the _lowest-cost truck_ for the _neediest shovel/route_ and issues the final instruction [25-27].

If the assignment engine lacks **accurate knowledge of the mine**—derived from inputs such as **GPS Position Data**, **Mine Model Configuration** (roads, elevations, compatibility), **Cycle Time Statistics**, and **Restrictions**—it is unable to make the best decisions, leading to poor assignments, unnecessary queuing, and increased costs [28-32].

Types of Assignments

Assignments are further classified by their trigger mechanism and execution scope [33]:

|   |   |   |
|---|---|---|
|Type of Assignment|Definition|Trigger/Mechanism|
|**Immediate Automatic**|Issued by the Assignment Engine instantly upon a critical event [33].|Triggered by haul cycle progression (finishing dumping/loading), major state changes (assignment delay ends), or misroute detection [13, 16, 34].|
|**Scheduled Automatic**|Enacted when a configurable time window is met, typically for non-production tasks [33, 35].|Triggered by timing settings (like the "Required At" time) or specific conditional thresholds (like low fuel level) [36, 37].|
|**Immediate Manual**|Overrides an existing assignment and takes effect instantly [38].|Initiated by the Mine Controller using utilities (like the Dispatch Utility or drag-and-drop on the Haul Route screen) [38-40].|
|**Manual Scheduled**|Deferred assignment enacted only when the truck reaches a specified activity or load state [33].|Set by the Controller (e.g., specifying the next destination while the truck is loaded) [35, 41].|

Manual assignments must be used sparingly because they **disrupt the DP logic and cycle time calculations**, risking poor data integrity, inaccurate travel times, and zero spot/load times [40, 42].

--------------------------------------------------------------------------------

## Haul Truck Assignment Logic and Optimization

The question "Where Will Assignments Send Me?" is answered by understanding the primary objective of the Assignment Engine (which encompasses the Linear Programming/LP and Dynamic Assignment/DP layers) and the numerous data inputs and configurations that determine the optimal destination for a haul truck.

The objective of the assignment process is to **maximize overall production** while simultaneously **minimizing cost** (primarily measured by travel time and idle time) by ensuring assignments comply with the current mine plan and all constraints imposed on the system [1], [2], [3], [4], [5].

Assignments send trucks to three main categories of destinations: a loading tool (shovel) to load, a processor (dump, crusher, or stockpile) to unload, or a station for other activities [6].

I. Assignment Priorities and Decision Hierarchy

The destination a truck is sent to is determined by a strict hierarchy of factors and constraints, with production goals serving as the primary drivers [2], [7], [8].

1. Optimization Goal (Production Requirements)

The system first checks if **Production Requirements** (goals, production rates, and equipment priorities) have been set [2].

• **If requirements are set (YES):** Assignments are primarily driven by achieving established production goals and adhering to shovel priorities [2].

• **If no production requirements are set (NO):** The system defaults to maximizing tons moved and minimizing cost, utilizing model configuration data and cycle time statistics (travel times, dynamic load times, and queue time tolerances) [2].

2. Overriding Priorities (Special Assignments)

Even when running automatic optimization, certain _special assignments_ related to critical operational needs take precedence over standard load/dump assignments:

• **Fuel Assignments:** If a truck's fuel level drops below the **Critical Fuel Level** threshold, the truck is assigned **immediately** to the nearest fuel bay, regardless of queue capacity [9], [10]. If the fuel is merely below the **Refuel Level**, the assignment is scheduled, and the system attempts to integrate the fueling stop optimally [11], [12], [13], [8].

• **Break/Scheduled Assignments:** Assignments for shift change or mandated operator breaks (tiedown assignments) are calculated based on the break schedule [14], [15]. For automatic scheduled assignments, the system ensures the truck arrives as close as possible to the **Required At** time [16], [17], [18]. The assignment algorithm may send a truck for an extra production cycle if the estimated total cycle time lands closer to the "Required At" time than going straight to the tiedown location [18], [19].

• **TKPH Assignments:** If a truck exceeds the **TKPH Warning Threshold**, the system attempts to assign the truck to shovels where the routes have the **lowest average TKPH value** (lowest 25% percentile of haulage cycles, if configured), restricting the truck from high-TKPH routes to minimize further tire wear [20], [21], [22].

II. Destination Determination (Load and Dump Assignments)

The destination chosen for a truck depends on whether it is empty (seeking a shovel/loading unit) or loaded (seeking a dump/processor) [6], [23], [24].

1. Empty Trucks (Assignment to Shovel/Loading Unit)

The DP layer selects the destination by finding the **neediest shovel** and the **lowest-cost route** leading to it [25], [4].

• **Neediest Shovel:** The shovel with the greatest deficit between **Required Haulage** (set by LP constraints, priorities, and coverage factors) and **Current Haulage** (trucks currently assigned to it) is selected [26], [27]. If multiple shovels are waiting (idle) longer than a defined threshold, the one with the highest calculated priority and production rate is chosen [28], [29].

• **Lowest Cost Route:** The truck is assigned to the shovel/route combination that results in the **lowest assignment cost** [4]. Cost is weighted to prioritize travel time (often 1.5texttimesmoreexpensivethanidletime) over waiting time, aiming for the shortest haul while avoiding excess queuing [30].

2. Loaded Trucks (Assignment to Dump/Processor)

For loaded trucks, dump assignments are typically triggered immediately upon completion of loading [4], [5], [31]. The system adheres to specific constraints before resorting to optimal routing:

1. **Hard Locks:** The highest priority is the **Dump Lock on Grade with Hard Lock enabled**; this forces the truck to tip at that exact dump, overriding all other locks [32], [33], [34], [35].

2. **Truck Dump Lock:** If a lock is applied directly to the truck (Truck Dump Lock), it overrides both shovel locks and standard grade locks, assigning the truck to that specific dump [32], [33], [36], [37].

3. **Shovel Dump Lock:** If the shovel is locked to a dump, trucks loaded by that shovel are assigned to that dump (unless overridden by a Truck Lock) [32], [33], [38], [37].

4. **Grade Dump Lock (Standard):** If a specific material grade is locked to a dump, the truck is assigned to any dump that accepts that grade [32], [33], [39], [35].

5. **LP Optimization:** If no locks apply, the truck is sent to the **neediest loaded LP Path** originating from the shovel that loaded the truck, provided the dump is **Ready** and **Accepts the material** [40], [41], [42], [36], [5]. This typically routes the truck to the closest appropriate dump [43], [42].

III. System Inputs that Control the Destination

The accuracy of all inputs is essential, as **without accurate knowledge of the mine, the system may send trucks poor assignments**, resulting in queuing, idle loaders, and misdirected loads [44], [45].

Key inputs and configurations directly impacting where a truck is sent include:

• **Mine Model Accuracy:** The assignment relies on the **Best Path (BP)** layer, which calculates routes based on the accurate locations and **elevations of all callpoints, blasts, and dumps**. Incorrect elevations can cause the optimizer to make "wrong" assignments by miscalculating travel times and gradients [46], [47], [48].

• **Availability/Status:** Equipment must be set to **Ready** or **Available for Assignment** to be considered as a destination [49], [50], [51], [52]. If a dump or processor is set to `Down` or `Standby`, assignments will not be sent to it [49], [53], [54], [55], [56].

• **Material Compatibility:** The intended destination (dump/processor) must be configured to **Accept the material type** the truck is hauling; otherwise, the assignment will fail [57], [58], [59], [60], [61].

• **Restrictions:** Locks and bars severely restrict destination options, ensuring trucks are sent only to or away from specific shovels, dumps, regions, or materials [62], [63], [64].

• **Dump Capacity:** The maximum capacity (tons/hr) and **Queue Capacity** (# of trucks allowed to queue) set for dumps limit the flow to prevent bottlenecks [65], [66], [67]. If capacity is reached, trucks are automatically diverted to other available dumps [65], [67].

--------------------------------------------------------------------------------

## Automated and Manual Truck Assignment Logic

The sources classify truck assignments into two main categories: **Automatic Truck Assignments** (the preferred mode of operation for optimization) and **Manual Truck Assignments** (used for intervention and control), with further differentiation based on timing and triggers. These assignments are the primary output of the Assignment Engine Logic, which draws on vast amounts of data inputs.

I. Automatic Truck Assignments (System-Generated)

Automatic assignments are issued by the optimization engine (DISPATCH or Joptimizer) without direct human intervention at the moment the assignment is generated. This is the optimal mode of operation for maximizing production and efficiency.

A. Immediate Automatic Assignments

These assignments are generated immediately when a specific, critical event (a trigger) occurs in the haul cycle or operational status of the equipment:

1. **Truck Finishes Dumping:** When the truck bed is lowered and the truck enters the **Traveling Empty state** (or activity), the system immediately generates an assignment to a loading unit.

2. **Truck Finishes Loading:** Once the loading tool operator sends a load report to the office, or the truck drives away more than 25 meters, the dump assignment is automatically generated.

3. **Truck’s Loaded Material Changes:** If the material loaded onto the truck differs from the material the truck was originally assigned to carry, a new assignment request is triggered to find a compatible dump destination.

4. **Truck Diverts from Assigned Path (Off-Course):** If the truck is detected as not following its assigned path (e.g., traveling more than 100 meters outside the route), the system triggers an automatic reassignment calculation.

5. **Assignment Delay Ends:** When an assignment delay on the truck ends (either manually or automatically), an automatic assignment to the next task is triggered.

6. **Truck Enters a Reassignment Waypoint:** Passing a specifically configured waypoint triggers a dynamic reassignment process if a better, non-backtracking alternative destination is found.

7. **Truck Starts Loading (Less Common):** If certain configuration options are enabled (e.g., suppressing start of service assignments is off, and assigning at start of loading is enabled), the start of loading can trigger an assignment.

B. Scheduled Automatic Assignments

These are assignments generated automatically based on pre-configured time windows or conditions that must be met. They typically involve non-production tasks:

1. **Time-Based Assignments:** These are enacted when a pre-configured time window can be met, and the truck’s load state matches given criteria.

2. **Refueling Assignments (Fuel Assignments):** The system dynamically assigns trucks to refuel based on fuel level thresholds. If the fuel drops below the "fuel low percent," the system starts considering assignments to nearby fuel bays. If it drops below the "fuel empty percent," the truck is assigned immediately, ignoring queue capacity at the fuel station.

3. **Break Assignments:** Joptimizer allocates trucks to tiedown locations when there is insufficient time to complete another load before a scheduled break.

4. **TKPH Assignments:** If a truck's Tonnes Kilometers Per Hour (TKPH) reading reaches a warning level, the truck may be assigned to routes associated with shovels that have lower average TKPH values for the haul, restricting the truck to the lowest percentile of haulage cycles (e.g., the lowest 25%).

5. **Fatigue Assignments:** A fatigued operator may be assigned to the nearest available operator change location.

II. Manual Truck Assignments (User-Initiated Intervention)

Manual assignments are directives given by the Mine Controller or operator to override the system's current logic or to force a specific action.

A. Immediate Manual Assignments

These assignments take effect instantly, overriding any existing automatic assignment, and are typically executed by the Mine Controller:

1. **Mine Controller Assigns:** The Mine Controller can set an assignment immediately using tools like the **Truck Assistant** or the drag-and-drop feature on the HaulRoute screen. This overrides the current automatic assignment immediately.

2. **Operator Assigns (Override):** The truck operator can press a dedicated button on the field computer screen to request an immediate automatic assignment, or manually select a new dumping location from the Jpanel if the mine site allows, which overrides the current assignment.

B. Manual Scheduled Assignments

These are assignments scheduled by the Mine Controller to take effect only once the truck reaches a specified activity or load state:

1. **Manual Scheduled Action:** The assignment is enacted when the truck’s activity changes to the specified activity or load state. They are typically used to direct a truck to a specific location (e.g., picking up a specific load or getting fuel) after completing its current activity.

2. **Delayed Assignment (Pre-assignment):** A manual assignment can be created as a "pre-assignment" while the truck is loaded, specifying the destination for the subsequent empty travel cycle. This is generally preferred over immediate manual assignments as it disrupts the Dynamic Programming (DP) logic less severely than forcing a change mid-cycle.

III. Impact on Assignment Engine Logic

The existence of these different assignment types and triggers demonstrates how the Assignment Engine Logic relies on comprehensive data inputs and strict hierarchy to function:

• **Priority and Constraints:** Manual assignments and certain automatic assignments (like scheduled fuel or maintenance) adhere to a **strict hierarchy of controls** and can override constraints set by the LP layer, such as shovel or grade locks. For example, a **Truck Dump Lock overrides all shovel locks or grade locks**.

• **Data Integrity Risk:** While essential for controlling operations, **Manual Assignments disrupt cycle times and DP calculations**, potentially leading to data integrity errors such as inaccurate travel times, zero spot or load times, and inaccurate dig rates.

• **Optimization Goals:** The vast majority of assignments are **Immediate Automatic Assignments** aimed at fulfilling the optimized production plan established by the Linear Programming (LP) layer, ensuring the assignment moves tonnage as cheaply as possible by minimizing costs associated with travel, spotting, loading, and waiting.

--------------------------------------------------------------------------------

## Dynamic Mining Assignment Logic and Triggers

The fundamental goal of the assignment engine (Dynamic Programming/Dynamic Assignment, or DP/DA) is to maximize mine productivity and minimize cost by continuously calculating and issuing instructions (assignments) to trucks, dictating where they should travel next [1], [2].

The question, "**How Do I Get an Assignment?**," is answered by identifying the specific **Assignment Triggers**—events that notify the system that a truck is ready for a new set of instructions—and explaining how the assignment engine leverages operational **Inputs** to make its complex decision [3], [4], [5], [6].

Here is a comprehensive discussion of the assignment triggers and the logic used to fulfill them:

I. Assignment Triggers (When a Truck Receives an Assignment)

An assignment is a set of instructions that tells the truck operator to travel to a loading tool, a processor (dumping point), or a station for other activities [7], [8]. Assignments can be broadly categorized as automatic (system-initiated) or manual (user-initiated) [9].

The automatic assignment is the preferred method of operation and is issued by the Assignment engine when one of the following triggers occurs [9], [4], [10], [11], [12], [13]:

A. Haul Cycle Progression Triggers:

• **Truck finishes dumping (Empty/Load Assignment):** An automatic assignment to a loader (shovel) is generated immediately when the truck finishes dumping and enters the **Traveling Empty** state [14], [15], [11], [16], [17]. The operator must typically press the truck icon ("Assign" or similar) after dumping to receive the new shovel assignment from the system [18], [19], [20].

• **Truck finishes loading (Loaded/Dump Assignment):** An assignment to a dump is typically generated when the loading tool operator sends a load report to the office, or when the truck detects it has traveled more than 25 meters from the loading point [21], [11], [22], [23].

B. Dynamic Reassignment Triggers:

• **Truck enters a reassignment waypoint:** Any pre-configured waypoint (or callpoint) entered by the truck triggers the system to re-evaluate the assignment. If a better alternative is identified (e.g., due to congestion or shovel delays), a new assignment is issued [4], [24], [12], [25], [26].

• **Truck diverts from the assigned path (off-course/misroute):** If the truck travels more than a configured distance (e.g., 100 meters default) outside the assigned route, the original assignment is invalidated, triggering a request for a new, optimal assignment [4], [11], [27], [28].

• **Truck's loaded material changes:** If the material type changes (either via the shovel operator or the Mine Controller), the existing dump assignment is revoked, and a new one is calculated to ensure the material is hauled to a compatible destination [12].

C. Equipment Status Change Triggers:

• **Assignment delay ends on the truck:** When a scheduled assignment delay (entered either onboard or in the office) concludes, the system triggers a new assignment to ensure the equipment returns to productive work [29], [4], [12].

• **Shovel or dump status change:** If a major change in status occurs for a destination (e.g., a shovel becomes Down or a dump becomes Ready), the LP layer recalculates the solution, and trucks en route or waiting may be reassigned or given new instructions based on the updated plan [30], [31].

D. User-Initiated Triggers (Manual Requests):

• **Truck operator requests an assignment:** The operator can press a dedicated "Assign" button on the onboard screen to request the next assignment directly from the office [4], [11], [32].

• **Mine Controller requests an assignment:** The Controller can manually trigger an assignment calculation using the **Request Assignment button** in the Truck Assistant utility, particularly to resolve failed automatic assignments [33], [34], [12].

--------------------------------------------------------------------------------

## II. Assignment Engine Logic & Inputs (How the Decision is Made)

When a trigger occurs, the Dynamic Assignment (DA) layer executes a rapid, real-time process to determine the specific assignment based on the overall strategy set by the Linear Programming (LP) layer [2], [13], [35], [36]. The overall goal is to maximize production by minimizing the components of the haul cycle, leading to cost minimization [1], [37].

A. Foundation of the Decision: The LP Master Plan

The assignment decision is governed by the **LP solution** (the theoretical "master plan") which computes haulage path flow rates based on various constraints, including mine topography, shovel digging capacities, dumping point limits, blending rules, and available truck haulage [38], [39], [40], [41], [42].

B. The Dynamic Assignment (DA) Process (Empty Truck to Shovel)

When an empty truck requests a shovel assignment, the DP/DA layer employs a "balance and synchronization" strategy [43]:

1. **Find Neediest Shovel:** DP determines which shovel has the greatest need for a truck. This is calculated using two methods [44], [45], [46]:

• **By Idle Time:** If a shovel (with Normal or High priority) has been idle (waiting for a truck) for longer than a configured threshold (e.g., `assignByIdle`), it is prioritized [45], [47]. The shovel with the highest product of idle time and feed rate is considered the neediest [45].

• **By Haulage Need:** If no shovel meets the idle time threshold, need is determined by calculating the **Total Haulage** requirement versus the **Current Haulage** (trucks assigned). The shovel with the greatest difference (highest deficit) is the neediest [48], [49], [50]. High priority factors and high coverage percentages increase required haulage, thus increasing a shovel's chance of being deemed needy [51], [48].

2. **Find Neediest Route:** Once the neediest shovel is identified, the system finds the specific empty haul route (dump-to-shovel path) leading to that shovel that has the greatest deficit between its Required Haulage and Current Haulage [49], [52].

3. **Find Truck with Least Cost:** The system then evaluates all available trucks to find the one that results in the **lowest assignment cost** for that neediest route [53], [52]. Cost is primarily calculated based on [53], [54], [52]:

• **Travel Time Cost:** Travel time is weighted (by a configurable `travelCost` factor) and calculated using dynamic rolling averages based on road segment gradient, load state, and speed [55], [56], [53], [57].

• **Queue Time Cost (Idle Cost):** Forecasted waiting time at the shovel is weighted (by a configurable `idleCost` factor) [53], [54].

• **Priority Adjustment:** The idle cost is divided by the Shovel Priority Factor, meaning higher priority shovels appear to have a lower overall assignment cost [58]. The best assignment is the one with the smallest calculated production loss [59].

4. **Check Restrictions and Execute:** The system performs a final check against truck restrictions (e.g., locks/bars) before allocating the lowest-cost truck to the neediest shovel [60]. This decision is immediately executed, updating the database, transmitting the assignment (including the calculated Best Path route) to the onboard panel, and generating a new Estimated Time of Arrival (ETA) [61].

C. Loaded Truck Assignment (Truck to Dump)

When a loaded truck requests a dump assignment, the system first checks for pre-existing constraints before consulting the broader LP plan [62], [63]:

1. **Hard Locks:** The highest priority lock is a **Dump Lock on Grade with Hard Lock enabled**, which overrides all other locks and forces the truck carrying that specific grade to tip at the designated dump [64], [65].

2. **Truck and Shovel Locks:** The system checks if the individual truck or the shovel it loaded from is locked to a specific dump [64], [62]. A **Truck Dump Lock** takes precedence over a shovel dump lock [64], [65].

3. **Neediest Loaded Path:** If no locks apply, the truck is assigned to the **neediest loaded LP Path** originating from the shovel that loaded the truck, provided the dump accepts the material and is Ready [66], [63], [67].

--------------------------------------------------------------------------------

## Inputs for Mine Assignment Optimization Logic

The **Assignment Engine Logic** within mine management systems like DISPATCH and Joptimizer relies on a comprehensive and dynamically updated set of **Assignment Decision Inputs** to achieve its core objective: minimizing cost and maximizing production by generating the most efficient truck assignments possible [1, 2]. These inputs span configuration parameters, real-time measurements, calculated metrics, and human-defined constraints, all integrated to inform the Linear Programming (LP) and Dynamic Assignment (DA) layers of the optimization process [2-7].

If the system lacks **accurate knowledge of the mine** derived from these inputs, the consequences include poor assignments, queues, idle loaders, misdirected loads, and higher costs per ton [8-11].

The sources classify these inputs across several key categories:

1. Planning and Production Requirement Inputs

These inputs define the overall strategic goals and constraints the optimization engine must adhere to, particularly governing the LP master plan [3, 12-15].

• **Production Plans (Goals and Priorities):** These numerical production objectives must be achieved during the course of the shift [12, 16-18]. The assignment logic prioritizes achieving these production goals. If production requirements are set, assignments are guided by Production Goals, Production Rates, and Equipment Priorities; if not set, the system defaults to maximizing tons and minimizing cost [3, 13]. The objectives function in LP maximizes production subject to these hauling requirements [15, 19].

    ◦ **Priorities:** Shovel priorities (High, Normal, Low) create a hierarchy for goals and determine the order of capacity allocation [12, 20-22]. High-priority shovels are starved last in under-trucked situations [20, 23].

    ◦ **Material Priorities:** These priorities apply to shovels digging a selected material type and influence assignments to favor routes with higher priority material or shovels digging that material [16, 22, 24-26].

• **Blending:** Detailed blending goals and associated Material Quality Parameters are required inputs to enforce blending constraints, ensuring the material flow meets specific composition limits at dumps (processors/stockpiles) [16, 27-30].

• **Target Dig Rate (TDR):** This is the excavation rate goal set for shovels. If the TDR is higher than the calculated shovel capacity, the TDR becomes the capacity constraint used in the LP calculation [31-33].

2. Physical and Operational Configuration Inputs

These static or semi-static inputs define the geography, limitations, and compatibility rules of the mine operation [2, 13, 34].

• **Mine Model Configuration:** This encompasses basic parameters like **Machine/Material Compatibility** (e.g., specifying which truck classes can load at which loader classes, and which materials dumps accept) [3, 34-38]. Errors here prevent the creation of feasible routes [39].

• **Spatial Mine Model and Road Network:** The physical layout, including **road network definition**, accurate **callpoint elevations**, and the location and placement of all destinations, are foundational inputs [3, 34, 40-42]. If callpoint elevations are incorrect, the optimizer may make "wrong" assignments by miscalculating travel times [41-43].

• **Restrictions (Locks and Bars):** These constraints fundamentally limit the choices available to the Assignment Engine [2, 13, 31, 44-48].

    ◦ **Equipment Restrictions:** Constraints imposed between specific trucks, truck types, shovels, and dumps (e.g., locking a truck to a dump) [31, 32, 49-51].

    ◦ **Grade Restrictions:** Rules ensuring material from a specific mining block is assigned to compatible destinations [48, 51].

    ◦ **Route Restrictions:** Defining one-way or closed paths on road segments [22].

• **Equipment Capacity and Availability:** This includes setting **Dump Capacity** (maximum tonnes per hour) and **Dump Queue Capacity** (maximum trucks allowed to queue) to prevent bottlenecking [13, 52-55]. Shovels and processors must be set as **Available for Assignment** to be included in the production plan [56-59].

• **LP Global Parameters:** Settings that dictate the overall philosophy of the LP model, such as whether to **Allow Unused Shovels in Undertrucked Situations (LPMATCHTRUCKS)** or use the **Global Truck Productivity Objective** to favor matching big trucks to big shovels [2, 60-63].

• **Standard Truck Size:** This calculated average size is critical for LP calculations, such as determining hauling requirements and solving LP solutions across mixed fleets [33, 63, 64].

3. Dynamic and Real-Time Measurement Inputs

These inputs are constantly flowing from the field to the control system, enabling instantaneous, dynamic decision-making by the DA layer [2, 4, 41, 42, 65].

• **GPS Position Data:** Provides the equipment’s real-time position, speed, and heading, which is fundamental for misroute detection, ETA updates, and Best Path selection [65-67]. Incorrect callpoint elevations corrupt this data [68].

• **Equipment Status and Activity State Transitions:** Real-time data confirming if equipment is **Ready, Down, Delay, or Standby** is continuously monitored [3, 65, 69]. Status changes on shovels or dumps are vital triggers for LP recalculations [70-72].

• **Cycle Time Statistics:** Dynamically updated time components such as **Travel Times** (empty and loaded), **Dynamic Load Times**, **Spot Times**, **Dumping Times**, and **Queue Time Tolerances** are critical inputs used to calculate the cost (lost tons) of prospective assignments [3, 13, 73-77].

• **Shovel Dig Rate and Truck Payload:** These are continuously measured to reflect current mining conditions (e.g., how fast the shovel is digging material) and truck load size [3, 69, 78-80].

• **Fuel Level and TKPH:** Fuel level triggers **automatic refueling assignments** based on configurable thresholds [13, 65, 81-83]. **Tonnes Kilometers Per Hour (TKPH)** calculations are based on load weight, distance, and time, influencing routing to minimize tire wear [13, 76, 84-87].

4. Special Assignment and Predictive Inputs

The assignment engine must integrate information used for non-production assignments and long-term planning [2, 13].

• **Scheduled Assignments:** Configuration details for necessary non-production tasks like fueling or shift change, often defined by **target times and load state triggers** (e.g., _Arrive Before_ or _Required At_ time) [13, 88-91].

• **Reassignment Waypoints:** Locations configured to **trigger assignment re-evaluation** if a better path is found due to changing conditions [2, 13, 92-94].

All these inputs collectively feed into the dual optimization layers: LP defines the theoretical master plan and capacity allocation, and the DA layer executes the real-time assignment based on instantaneous conditions and calculated costs [6, 95, 96].

--------------------------------------------------------------------------------

## User Impact on Mine Fleet Optimization

The concept of "**What Do I Impact?**" directly addresses the critical role that various users—particularly the Fleet Users (Mine Controllers and Builders)—play in maintaining the integrity of the mine data and configuring the system inputs, which directly dictates the behavior and effectiveness of the Assignment Engine Logic.

The sources emphasize that the optimization software (such as Cat MineStar System Fleet or Joptimizer/DISPATCH) relies heavily on accurate knowledge of the mine, and this knowledge often comes from user interaction and configuration. Without accurate knowledge, the system makes suboptimal decisions, leading to queuing, idle loaders, misdirected loads, higher costs per ton, and affected production recording [1-10]. Conversely, when the system has accurate knowledge, benefits include improved machine utilization, reduced dilution, improved recovery, reduced costs per ton, and precise real-time Key Performance Indicators (KPIs) [1-10].

The "What Do I Impact?" concept lists the key system elements where user actions and correct inputs directly affect the Assignment Engine's calculations and decision-making processes.

Key Areas of Impact on Assignment Engine Logic & Inputs

The sources list 11 major areas where users directly influence the optimization strategy and assignment output:

1. **Mine Model Configuration**This involves setting up parameters like machine and material compatibility, loading tool and processor design times, machine and processor availability, and processor nominal rates [11]. Configuring dump locations to accept specific material types is crucial, as this directly affects route feasibility for loaded trucks [12-15]. The assignment engine removes unavailable machines from the production plan [16].

2. **Spatial Mine Model**Users must ensure the alignment of the model with the site surface, perform regular model checks (e.g., using Exception View), and verify the correct placement of destinations [11]. Errors in callpoint elevations, for example, can cause the optimizer to perform "wrong" assignments by miscalculating travel times [17-19].

3. **Reassignment Waypoints**This involves the regular review of the placement and purpose of reassignment waypoints (callpoints) [11]. Their strategic placement triggers the system to re-evaluate a truck's assignment based on dynamic mine conditions [20].

4. **Cycle Time**The system relies on accurate cycle time components for assignment decisions [21, 22]. User actions directly impact this through setting the **Dynamic Efficiency Factor** on loading tools [10]. Incorrect actions, such as trucks stopping on haul roads without the operator entering a delay, corrupt the dynamic travel time of the road segment, leading to improper assignment calculations [21, 22].

5. **Assignment Groups**These groups must be used appropriately according to the daily mine plan to define regional constraints or specialized haulage circuits [10, 23]. Proper use helps segment the fleet and prevents unnecessary complexity for the optimization algorithm [24].

6. **Restrictions**This critical area involves applying and minimizing the use of **Locks**, **Bars**, and **Provisional Locks and Bars** (including mining block locks), as minimizing unnecessary restrictions generally improves production outcomes by giving the system flexibility [10, 25, 26]. Locks and bars enforce specific assignments (e.g., locking a shovel to a dump) or prohibit certain assignments (e.g., barring a truck from a specific material or shovel) [25, 27].

7. **Production Requirements**This relates to setting production goals and priorities correctly. When utilized properly, these settings guide the assignment engine's objective function (Linear Programming layer) to meet specific output targets and allocate capacity [10, 28, 29].

8. **Blending**Users define blend models and monitor them continuously. Accurate blend definitions are required so that the assignment algorithm can ensure material flows meet specific quality criteria [10, 30, 31]. If material flows risk violating blending limits, the assignment logic shifts to correct the composition [32].

9. **Delays**Accurate delay management is vital. This includes correctly recording current and future delays, setting planned durations, distinguishing between assignment and non-assignment delays, and handling delay over-runs [10, 33, 34]. Correct delay configuration influences whether the assignment engine factors a machine's delay into its future planning [35-37].

10. **Scheduled Assignments**The configuration of scheduled assignments (such as shift changes or fueling), including setting target times and load state triggers, and applying on-arrival delays, directly influences when and where a truck is pulled from production for essential non-haulage tasks [38, 39].

11. **People**The behavior of operators and controllers influences data quality. This includes ensuring correct system usage (e.g., stopping delays when work resumes), adherence to assignments, and following defined site procedures [38, 40]. Manual assignments, for instance, disrupt Dynamic Programming (DP) logic and lead to errors like zero spot times or inaccurate travel times, highlighting the negative impact of human intervention on automated processes [41-43].

--------------------------------------------------------------------------------

## Automatic and Manual Triggers in Mine Haulage Assignment

The sources identify a variety of automatic and manual events, known as **Assignment Triggers**, that prompt the assignment engine (Dynamic Programming, DP, or Dynamic Assignment, DA) within the Optimization Strategy (DOS/Joptimizer/DISPATCH) to recalculate and issue a new assignment to a haul truck or piece of equipment. These triggers are critical for maintaining the efficiency and accuracy of the system by ensuring truck assignments reflect the constantly changing operational realities of the mine.

The system relies on real-time data inputs and continuous monitoring to detect these events and execute assignment logic immediately [1-3].

Categories of Assignment Triggers

Assignment triggers can be broadly categorized based on their origin: system-detected events (automatic monitoring) or direct user intervention (manual requests).

I. Automatic, System-Detected Triggers

These triggers are derived from sensors, GPS data, payload systems, or internal logic detecting changes in the truck's operational cycle, material status, or environment [4-8].

|   |   |   |
|---|---|---|
|Trigger|Description & Mechanism|Context / Resulting Assignment|
|**Truck Finishes Dumping**|The most typical trigger for an empty truck assignment occurs when the truck completes its loaded phase. This is detected when the truck bed is lowered (Dump switch is OFF/OEM Tray Down input) and the truck enters the **Traveling Empty state** or activity [4, 7, 9-13]. The operator often presses a button to receive the assignment [4, 11].|**Immediate Automatic Assignment** to load is generated, aiming for the neediest shovel based on the optimized LP Solution [4, 10, 13, 14].|
|**Truck Finishes Loading**|An assignment to dump is triggered once the shovel operator communicates that loading is complete (Load report sent to the office) or when the truck moves a certain departure distance (e.g., 25m) [5, 15, 16]. This usually generates the dump assignment [17, 18].|**Immediate Automatic Dump Assignment** is generated based on the material loaded and destination constraints [4, 16, 17, 19].|
|**Truck’s Loaded Material Changes**|Occurs if the loading tool operator changes the material being loaded on the truck, or if the Mine Controller manually updates the material in the Truck Assistant [6]. This is critical as it affects dump compatibility [4, 20, 21].|**Immediate Automatic Dump Assignment** calculation is re-initiated to find a compatible dump location for the new material grade [4, 6, 20, 21].|
|**Truck Diverts from Assigned Path (Misroute)**|Generated if a truck travels more than the configured distance (default 100 meters) outside the assigned route (the "green line"). The onboard system sends a reassignment request to the server [5, 6, 22, 23].|**Automatic Reassignment Process** begins. If the misroute message is detected as too old (e.g., over 30 seconds), the server may ignore it, assuming the problem was solved locally [4, 5, 24, 25].|
|**Truck Enters a Reassignment Waypoint (Callpoint)**|Occurs when a truck passes through a waypoint specifically configured to initiate assignment re-evaluation (ReAssign when Loaded or ReAssign when Empty) [6, 26-30].|**Automatic Reassignment** occurs if the system identifies a better alternative that minimizes cost (travel and idle time) and does not require the truck to backtrack [31-34].|
|**Assignment Delay Ends**|When an expected delay (entered by the operator onboard or the Controller in the office) on the truck expires [6]. The status may change from Delay back to Ready [35, 36].|**Immediate Assignment** is triggered to assign the truck to its next appropriate task (e.g., shovel assignment or scheduled fuel/break assignment) [4, 6, 37].|
|**Scheduled Automatic Assignment Triggers**|Assignments (like refueling or maintenance) triggered when pre-configured time windows are met and the truck's load state matches the criteria [4, 19, 38]. The "Required At" time is the primary factor [39].|**Scheduled Assignment** overrides normal production assignments. For critical fuel levels, the assignment is immediate, disregarding fuel bay queue capacity [40-45].|
|**Backup Assignment for Poor Comms**|If the truck enters an area with configured "Bad Communications," the system issues two assignments: one for travel into the area and one (hidden) for the return journey [46].|**Backup Assignment** provides instructions for traveling through an area where reliable real-time updates are not expected [46].|
|**Truck Starts Loading/Full**|Although less common, assignment may trigger at the start of loading if "Suppress start of service assignments" is off and "Assign at Start of Loading Enabled" is checked [5]. The shovel pressing FULL (load-out) triggers the dump assignment for the truck [17, 18, 47].|**Immediate Automatic Assignment** (dump assignment) [4].|

II. Manual, User-Initiated Triggers

These triggers originate from the operator on the field computer or the mine controller in the office application (MineView/Jmineops), deliberately interrupting or advancing the automatic flow [1, 5, 29, 48].

|   |   |   |
|---|---|---|
|Trigger|Origin & Mechanism|Context / Purpose|
|**Truck Operator Requests Assignment**|Operator presses an "Assign" button on the onboard screen [5, 49, 50]. This sends a request/response TMAC message to the server [51].|Used to obtain a new assignment immediately, often after dumping or if the previous automatic assignment was missed [4, 50].|
|**Mine Controller Requests Assignment**|Controller presses the **Request Assignment button** in the Truck Assistant utility [5, 49, 52, 53].|Used after troubleshooting a failed assignment, or to override the current automatic assignment, forcing the DP logic to calculate the next optimal choice [1, 5].|
|**Mine Controller Manually Assigns Truck**|Dispatcher uses the Truck Utility or drag-and-drop on the HaulRoute screen to issue an immediate or delayed assignment [1, 54-58].|Manually overrides automatic assignments. **Warning:** Manual assignments disrupt DP logic and cycle time calculations, leading to data integrity issues like zero spot times or inaccurate travel times [54, 59-63].|

Role of Inputs in Triggering Assignments

The assignment engine relies heavily on continuous data flow to verify the state changes required for triggers:

1. **Status and Availability:** Assignment decisions depend on equipment statuses (Ready, Delay, Down, etc.) [64-66]. A change in a shovel or dump status to "Ready" or "Down" triggers an LP recalculation and subsequent DP assignment review [37, 67]. Machines must be explicitly marked as **Available for Assignment** to be considered [68-71].

2. **GPS and Network Health:** Triggers like Misroute Detection and successful execution of assignments rely on accurate GPS Position Data [5, 72, 73] and connectivity via TMAC messages [74, 75]. Poor GPS or communication integrity hinders the ability of the system to correctly identify state changes and execute reassignments [76, 77].

3. **Physical Cycle Completion:** The system tracks precise movements and sensor inputs (OEM payload systems, Dump switches) to accurately mark the transition between activities (e.g., Loading, Tipping, Traveling Empty), creating the fundamental triggers for the next cycle's assignment [7, 9, 78].

--------------------------------------------------------------------------------

## Haul Truck Dump Completion Assignment Trigger

The event where a **truck finishes dumping** is a highly critical **assignment trigger** that signals the end of the loaded portion of the haul cycle and immediately initiates the process for assigning the empty truck to its next loading destination (shovel).

This trigger fundamentally shifts the truck's state and activity, prompting the Dynamic Assignment (DA) layer of the optimization system (DISPATCH/Joptimizer) to generate a new, optimized assignment.

1. Mechanism of the Dump Completion Trigger

The actual trigger for the system occurs when the truck physically completes the tipping activity and transitions into the next major activity state:

• **State Change:** The definitive trigger for assignment generation is when the truck’s bed is lowered, causing the truck to enter the **Traveling Empty state** or activity [1-9].

• **Haul Cycle Definition:** The haul truck's productive cycle is defined as running _from_ Traveling Empty _to_ Dumping [10-12]. Therefore, the act of finishing dumping and transitioning to Traveling Empty marks the definitive beginning of the subsequent cycle's assignment process [1, 3, 5, 13].

• **Operator Input (Required):** Once the haul truck finishes dumping, the **truck operator presses the truck icon (or Assign)** to receive a new shovel assignment from DISPATCH [14, 15]. This operator action is necessary to trigger the system to provide the shovel assignment [4, 16, 17].

2. Physical and System Detection of Dumping Completion

The transition into the Traveling Empty state, which triggers the need for a new assignment, is detected through mechanical and GPS inputs:

• **Dumping Activity:** The **Dumping activity** itself consists of two state changes: raising the bed (Queueing Loaded to Dumping Loaded state transition) and **lowering the bed** (Dumping Loaded to Traveling Empty state transition) [18, 19].

• **Inputs:** The Tipping activity (dumping) is deemed complete when:

    ◦ The **Dump switch is OFF and the truck is traveling** at a speed ofgeq15textkph [20].

    ◦ The **Maximum dumping duration of three minutes** is reached [20, 21].

    ◦ **More than 15 seconds after the dump switch has been deactivated** [20].

    ◦ The **OEM system detects Tray Down** (for trucks with active payload systems like VIMS) [22].

• **Backup Mechanism (End Point D Beacons):** If the dump switches on the truck are non-functional, the dump location can be configured with an **End Point D (Departure) Beacon**. This configuration ensures that a new assignment is triggered the moment the truck leaves the beacon's area of influence after dumping [23-26].

3. Assignment Generation After Dumping

Once the dump completion trigger is recognized, the Dynamic Assignment (DA) layer immediately addresses the need for the empty truck's next task:

• **Immediate Assignment Calculation:** When the truck dumps and is rechecked at the Traveling Empty state, an **automatic assignment to load is generated** [3, 13]. The system calculates and sends the next assignment immediately to avoid confusion, even if the optimal assignment might slightly change later [13].

• **Assignment Priority Check:** The DA layer generates the optimal assignment by first checking for any mandatory assignments (such as scheduled **fueling assignments** or **scheduled maintenance**). These special assignments are triggered if the truck's fuel level falls below critical thresholds or if maintenance intervals are met, and they **override DP assignments** to a shovel [27-29].

• **Haulage Optimization:** If no special assignment is necessary, the DA layer uses the LP solution as its master plan to calculate the assignment that minimizes production loss (cost) by sending the truck to the **neediest shovel** [30, 31].

The successful completion of the dumping activity is vital for data integrity; inaccurate dump times can affect cycle time calculations, and incorrect reporting of this phase can negatively impact productivity monitoring [32, 33].

--------------------------------------------------------------------------------

## Truck Operator Assignment Request Mechanism

The sources clearly identify a **truck operator requesting an assignment** as a key **event-based trigger** that prompts the assignment engine (Dynamic Programming/Dynamic Assignment Layer, or DP/DA) to generate a new automatic assignment for the truck [1-7]. This action typically occurs when the operator presses a specific button on the onboard field computer screen [4].

Context of the Trigger in the Haul Cycle

The process of the operator requesting an assignment is primarily tied to a major state change in the truck's haul cycle, indicating the truck is ready for its next productive task [2].

**When the Request is Triggered:**

• **During Traveling Empty State:** An automatic assignment to load is typically generated once the truck finishes dumping and enters the **"Traveling Empty"** state [3, 8-12]. However, the operator may manually request an assignment if the pre-calculated one is missed or delayed.

• **Post-Haul Cycle Completion:** After the truck finishes dumping its load, the operator may press the appropriate field computer button (often designated as "Assign") to receive a new shovel assignment from DISPATCH [13-15].

• **Logon/Start of Shift:** At the beginning of a shift, after the operator logs on and changes the truck status to **"Assign"** (Ready), the central computer sends the initial assignment message [6, 16-19]. The physical assignment request occurs when the truck operator presses the **"Assign"** button on the screen [4, 20].

Mechanism of the Assignment Request

When the truck operator presses the dedicated button on the panel, it sends a request to the office software to calculate and issue the next optimal assignment [4, 6, 7].

• **Communication Flow:** The operator initiates the request by pressing a button on the screen [4, 6]. This action generates an assignment request, which is a type of message that the application server then processes [2, 4, 21-23].

• **TMAC Messages:** This request is typically transmitted via **Request/Response TMAC (Trimble, Mincom, Aquila, Caterpillar) messages** over the wireless network. This type of data flow is generally used for requests of information (like an updated assignment) by the field machine, which requires a response from the application server [21, 24, 25].

• **Troubleshooting:** The ability for the operator to manually request an assignment (or the dispatcher to do it via the office) is a necessary action when the automatic assignment fails for troubleshooting purposes [2, 3, 6].

Consequences of Operator Request in Different Scenarios

The system responds to the operator request by calculating the optimal choice based on the current mine status and constraints:

• **Empty Truck Assignment:** If the truck is empty and requests an assignment, the DP layer uses the latest LP solution as its master plan to determine the neediest shovel and the lowest-cost route to meet the optimal flow rates [5, 6, 26, 27].

• **Loaded Truck Assignment:** If the truck is loaded (e.g., operator pressed the wrong button and forced the assignment event), the DP model performs loaded assignment logic, checking dump locks (truck, shovel, or grade) and material compatibility before assigning the truck to the neediest loaded LP Path originating from the loading shovel [28-30].

• **Disruption to Logic:** Manual assignments requested by the operator disrupt the standard DP logic flow and cycle time calculations, which can potentially lead to errors in data integrity, such as inaccurate travel times or queuing [31, 32].

Ultimately, the operator's request acts as an immediate command to the system to calculate the absolute best and most efficient assignment at that moment, overriding the default passive waiting mechanisms [2, 3, 6, 33].

--------------------------------------------------------------------------------

## Mine Controller Triggered Assignment Mechanism

The sources clearly identify a **Mine Controller requests assignment** as an **event-based trigger** that prompts the assignment engine (Dynamic Programming or DA Layer) to calculate and issue an immediate assignment to a truck [1], [2], [3], [4], [5].

This functionality is crucial when automatic assignment processes fail or when the Mine Controller needs to manually override the optimization system for specific operational reasons.

Mechanism and Location of the Trigger

The Mine Controller executes this trigger primarily through the office software utilities:

1. **Request Assignment Button:** The assignment is triggered when the **Request Assignment button** is pressed in the **Truck Assistant** utility [6], [7], [8], [4], [5], [9].

2. **Schedule Action Field:** In the older DISPATCH Utility, the Controller can select the "Assign" action from the **Schedule Action field** menu to request an assignment [10], [11].

When this button or option is used, the system calculates the optimal assignment for the empty truck [7], [12], [9].

Context of Use

This manual request is typically made in response to situations where a truck is ready for its next task but has failed to receive an automatic assignment [7]:

• **Troubleshooting Failures:** If a truck is empty but the automated system failed to assign it to a loader (appearing red on the Travel Progress Monitor or Fleet Update Assistant), the Controller must troubleshoot the root cause (e.g., restrictions, incompatibilities, poor communication) and then explicitly **request an assignment** once the issue is believed to be resolved [7], [12]. If the requested assignment fails again, the Controller must review the assignment context and continue troubleshooting [7], [13].

• **Overriding Automatic Logic:** The Controller may use this functionality to override automatic assignments. In fact, selecting the Request Assignment button in the Truck Assistant overrides the existing assignment, making it available for the next optimal assignment calculation [14], [9].

Manual Assignment Distinction

While requesting an automatic assignment is a key function of the Controller, it is distinct from certain manual operations:

• **Manual Assignment:** The Mine Controller has the capability to issue an **Immediate Manual** assignment directly, which overrides the existing automatic assignment immediately [15], [14], [16].

• **Automatic Trigger:** Requesting an assignment via the button triggers the automated DP logic to calculate the most optimal assignment based on all current mine constraints and the LP solution [12], [9].

In essence, the "Mine Controller requests assignment" trigger acts as a critical interface point where human oversight intervenes in the automatic optimization process to ensure that equipment quickly receives a functional assignment, especially following a disruption [17], [7].

--------------------------------------------------------------------------------

## Misroute Detection and Truck Reassignment Dynamics

The sources identify a truck diverting from its assigned path, or going **off-course (misroute detection)**, as a crucial **event-based trigger** for the optimization system (DP/DA layer) to initiate a reassignment calculation to maintain efficiency and control over the haulage cycle.

The Mechanism of Misroute Detection

A truck diverting from its assigned path alerts the system that the original assignment may no longer be viable or optimal, forcing a real-time recalculation.

1. **Trigger Event:** An automatic assignment is triggered when a **truck is detected as off-course (not following its current assignment)** [1].

2. **Path Monitoring:** The system monitors the truck to ensure it follows the assigned sequence of waypoints [2].

3. **Detection Threshold:** A misroute event is generated if the truck travels **more than 100 meters outside the assigned route** (the default value of the configurable `misrouteDistance` setting) [3]. This detection relies on the GPS tracking the truck's absolute position on the road segment [4]. If a truck travels off the assigned path for a configured distance, the system considers the original assignment invalid, and a misroute exception is logged [3].

4. **Misroute Exception:** When the off-course condition is met, the onboard JAMS system on the truck sends a request to the central server to be reassigned, resulting in a **misroute exception** being sent to the Mine Controller Inbox [3, 5, 6]. This exception alerts the Mine Controller that the truck is not following the expected DISPATCH assignment [6, 7].

System Response to a Misroute (Reassignment Process)

When a misroute event occurs, the system initiates the reassignment process to determine a new optimal path:

1. **Assignment Request:** The misroute event generates a request to the central server for the truck to be immediately reassigned [3].

2. **In-Range Requirement:** If the truck was temporarily **out of network range** when the misroute occurred, the notification will not reach the central server until the truck returns to wireless coverage [8, 9].

3. **Ignoring Stale Data:** If the misroute message arrives at the server but the time lapse since the event generation is **greater than 30 seconds** (the configurable `misrouteReassignTime`), the central system assumes the problem has been solved locally and **will not perform the reassignment** [8, 10].

4. **Reallocation Process:** If the misroute notification is recent (within the `misrouteReassignTime`), the system executes the reallocation process, seeking a new destination that minimizes cost (travel and idle time) and avoids backtracking [8, 11, 12].

Causes and Impact of Misroutes

Misroutes indicate a break in the planned haul cycle, disrupting the optimization algorithms:

• **Causes:** Misroutes are generally caused by operators taking an **unassigned route** or an **incorrect turn at an intersection** [9, 13]. Incorrect road network configuration or non-existing routes in the mine map can also cause misroute errors [10, 14, 15].

• **Data Integrity Impact:** Misroutes affect truck assignment algorithms because the system relies heavily on knowing the trucks' exact position within the haul cycle to make optimal decisions [10].

• **Preventive Measures:** Mine Controllers can configure the system to **"Ignore Misroutes"** if they believe the off-track driving is a simple indiscretion (e.g., "Comfort Stop") and should not trigger the reassignment logic [16, 17]. However, misroutes, if not corrected, lead to operational problems and are tracked as a key operational KPI: **Misroute Rate** [18].

• **Troubleshooting:** If numerous misroutes occur for a single truck, it usually indicates issues with the **onboard database** of the truck, possibly requiring reloading the database and restarting the panel [19]. Dispatchers are advised to consult the **Assignment Context** to determine why the truck was not following the assignment [20, 21].

In summary, misroute detection serves as a vital safeguard, forcing the reassignment engine to correct deviations from the calculated optimum path as quickly as possible, provided the data received about the misroute is not considered stale [1, 8].

--------------------------------------------------------------------------------

## Material Change as Assignment Trigger in Mining

The sources identify **a truck's loaded material changing** as a specific **Event-based Trigger** that causes the assignment engine (Dynamic Assignment/DP layer) to re-evaluate the truck's intended dump assignment, especially if the new material type conflicts with the current route or destination constraints [1-4].

When this trigger occurs, it initiates a re-evaluation to ensure the truck is correctly routed to a destination that accepts the newly loaded material, thereby maintaining production integrity and adhering to material blending constraints [5-7].

Causes and Mechanisms of Material Change as a Trigger

A change in the loaded material initiates a sequence of checks and calculations critical for the subsequent loaded truck assignment:

1. **Operator Action (Shovel/Loader):** The material assignment on a truck is typically determined by the shovel operator [8-14]. When the shovel finishes loading a truck, the shovel operator communicates the material type (and grade, if applicable) [8, 10, 11, 15]. If the shovel operator changes the material type being loaded onboard, this acts as an assignment trigger for the truck [2].

2. **Controller Intervention:** The Mine Controller can manually change the truck's material information in the **Truck Assistant** utility (General tab) [16-18]. Updating the material information and clicking "Apply" will automatically trigger an assignment re-evaluation [2, 18, 19].

3. **Conflict with Assigned Material:** An assignment request is generated if the material in the load report received from the loading tool is **different from the material the truck was originally assigned to carry** [1].

4. **Automatic Detection (Loading System):** Once loading starts, if a truck has a working payload system (e.g., VIMS/TPMS), the system detects the increase in weight [20]. If the shovel operator places a truck into the hauling activity, the system transmits the material type from the shovel to the truck, which then generates the correct dump assignment [14, 21].

5. **Reassignment Trigger:** The change in loaded material acts as a direct trigger for reassignment, along with events like changes in shovel or dump status, or misroute detection [4, 22].

System Response and Assignment Logic

Upon detecting a change in loaded material, the Dynamic Assignment (DA) layer executes the logic necessary for generating a valid dump assignment [23, 24].

• **Material Acceptance Validation:** The system must verify if the **newly loaded material** is allowed at candidate dump locations [5, 7, 25]. If the material loaded onto the truck is not explicitly configured to be accepted at the designated dumping point, the assignment fails, often resulting in a "Material Not Allowed at Processor" error [26-29].

• **Grade Blocks and Restrictions:** The material loaded is typically tied to a specific **grade block** [6, 25]. If **Grade Restrictions** exist—for example, if a specific grade block is locked to a particular dump—DP enforces this hierarchy, overriding shovel or truck locks if the grade block lock has a higher hierarchy level, or if "Hard Lock" is enabled [23, 30-33].

• **Dump Assignment Priority:** If a truck successfully changes its material type, the DP layer proceeds to generate a dump assignment. The logic checks for any overriding dump locks (Truck, Grade, or Shovel locks) and material acceptance constraints [5, 23, 24, 34]. If no specific lock applies, the truck is assigned to the **neediest loaded LP Path** originating from the loading shovel that accepts the material [23, 24, 30, 35].

If the assignment fails due to material configuration issues, the Assignment Context window will display the incompatibility, often necessitating the Controller to update the material information in the Truck Assistant and request a new assignment manually to dump the load [17, 18, 36, 37].

--------------------------------------------------------------------------------

## Assignment Triggers and Dynamic Delay Management

The end of an assignment delay is explicitly identified in the sources as an **Assignment Trigger** that causes the automatic assignment engine (Dynamic Programming/DA Layer) to recalculate and issue a new assignment to the affected equipment.

I. Role as an Assignment Trigger

When an assignment delay ends, the termination of that delay acts as a catalyst for the system to re-evaluate the equipment's status and subsequent tasking [1]. This is necessary because the equipment is transitioning from an "Inactive" state back to being ready for productive work [2], [3].

Specific triggers related to the end of a delay include:

• **Assignment Delay Ends:** An automatic assignment is triggered when an assignment delay on the truck ends [1]. The delay can be ended either by the operator on board or by the Controller in the office [1].

• **Automatic Status Change:** Trucks enter the Inactive activity after a status change that may be caused by arriving at a location like a shop or tiedown with an auto status change defined [2]. When the delay ends, the status changes, and a new assignment is needed.

• **Scheduled Automatic Assignments:** If a truck has a scheduled automatic assignment (often for fueling or shift change), this assignment will be triggered when the appropriate **Load State is met** and the required time window for the scheduled assignment is reached [4], [5].

II. Impact of Delays on Assignment Logic

The assignment engine logic is specifically designed to handle delays that occur on both trucks and loading units, actively predicting when a delay will end to maintain optimal flow [6], [7].

1. Predictive Assignment Planning

The Assignment Engine considers the expected duration of a delay when making assignment decisions, demonstrating its forward-looking planning capability:

• If a loader (shovel) goes on delay for a set duration (e.g., 15 minutes), the assignment generator will evaluate this information and **may still send trucks to that loader** if it anticipates the trucks will arrive around the time the loader is scheduled to come off delay [6], [8], [9]. This prevents the loader from subsequently experiencing _shovel hang time_ (waiting for trucks) [6], [10].

• When a truck is in a hauling activity and goes into a delay, Jmineops adds the expected delay time to the current **Estimated Time of Arrival (ETA)** to provide an updated completion time for the current activity [11], [12].

2. Configuration Based on Delay Type

The outcome of a delay ending often depends on how the specific delay type was initially configured:

• Some delays are configured so that when the delay is over, the machine **receives an assignment** (e.g., a truck coming off lunch or shift change delay) [13].

• Other delays are configured so that when the delay is ended, **an assignment is** **not** **given** (e.g., a truck coming off a ‘waiting for equipment’ delay) [13].

• A global setting can also dictate behavior: the **Make Assignable On Delay Start/End** option, when selected, ensures that the assignability status is reset to "ON" when a delay is started or stopped on the loading tool [14].

3. Consequences of Improper Delay Management

If an operator fails to manually **stop a delay** before resuming work (operator non-compliance), the system continues to assume the machine is delayed. This negatively affects the system's ability to make optimal assignments, potentially causing assignment failures and queuing [15], [16].

The correct procedure for changing an active delay in the office is to start the new delay _before_ manually stopping the old delay. This is critical to ensure **no time gap** occurs between the delays, as starting the second delay automatically stops the first one, maintaining data integrity [17], [18].

III. System Records and Troubleshooting

The conclusion of an assignment delay is recorded in the system's time management model and helps dispatchers monitor performance:

• **Status Records:** Each time equipment changes its operating status (Ready, Delay, Standby, or Down), a status record is created in the shift data table, which includes a timestamp and the reason code for the status change [19], [20].

• **Troubleshooting:** The termination of a delay can be reviewed in the **Status and Activity Gantt View** [21], [22]. If a change is needed (e.g., correcting an incorrectly entered delay duration or reason code after the delay has ended), the Controller can edit the historical delay record in the office, which automatically synchronizes the delay information with the corresponding cycle data [23], [24].

--------------------------------------------------------------------------------

## Dynamic Truck Assignment Trigger: Reassignment Waypoints

The sources establish that a **truck entering a reassignment waypoint** is a specific **event-based trigger** that causes the Dynamic Assignment (DA) layer (also referred to as Dynamic Programming or DP) of the optimization system (DISPATCH or Joptimizer) to evaluate and potentially change the truck's current assignment to a more optimal destination [1-7].

Here is a detailed discussion of the function, placement, and purpose of reassignment waypoints:

I. Function as an Assignment Trigger

The moment a truck enters a configured reassignment waypoint, it invokes the central office software to perform a new assignment calculation [3, 8].

• **Request for Re-evaluation:** This trigger sends a request to the office, similar to a request made by the truck operator pressing an "Assign" button, prompting the system to recalculate the next optimal destination [3, 9].

• **Dynamic Reassignment:** The primary purpose is to allow for **dynamic reassignment** of the truck while it is en route if conditions have changed (e.g., due to shovel delays, slowdowns, or availability changes) and a better alternative is identified [4, 5, 10-12].

• **Scope:** Reassignments are evaluated for trucks traveling empty to shovels or traveling loaded to dumping points [10, 11, 13].

II. Configuration and Placement of Reassignment Waypoints

Reassignment waypoints are standard beacons (locations) that have been specifically configured in the system for this purpose [5, 14, 15].

• **Configuration:** Any standard waypoint can be configured as a reassignment waypoint by selecting the appropriate checkbox in the **Waypoint Editor** [15]. The reassignment trigger depends on the truck's load state, meaning the waypoint can be set to **ReAssign when Loaded**, **ReAssign when Empty**, or both [13].

• **Strategic Placement:** These waypoints are intentionally placed at **strategic points** within the mine, often just **before major intersections** or sections of roads that commonly become congested [4, 16-18].

• **Purpose of Placement:** Placement before key decision points allows the system to recalculate and issue a new assignment message before the truck commits to turning onto a suboptimal route [4, 16]. This ensures the assignment has time to reach the operator so they can react accordingly [18, 19].

III. Logic and Constraints during Reassignment

When a truck hits a reassignment waypoint, the system evaluates all possible assignments based on lowest cost (smallest production loss) [20, 21].

• **Shovel Eligibility:** The system filters out ineligible shovels, including those that are disabled, restricted, or expected to have their queue capacity exceeded when the truck arrives [22].

• **No Backtracking:** A major condition for reassigning to a new destination is that the truck **must not be required to backtrack** or travel in the opposite direction from its current location [23, 24]. This ensures an optimal path is taken [23].

• **Optimization:** The decision to reassign is based on a cost calculation that compares projected travel time and potential idle time/queue time at candidate locations, factoring in shovel priority [25-27]. If a _better alternative_ is found, the new assignment is sent [4]. The system can be configured to **prohibit U-turns** during reassignment, if required [15, 28].

IV. Avoiding Improper Use

Misuse or incorrect placement of these waypoints can negatively impact system efficiency:

• **Sparing Use:** Dispatchers are advised to **use reassignment waypoints sparingly** to prevent unnecessary computations and radio traffic [15, 29].

• **Avoiding Intersections:** They should **not be placed directly at or immediately before an intersection** because the system needs time to recalculate and transmit the new assignment to the truck's onboard computer [17, 28]. The operator may miss the turn before receiving the updated instructions [17, 28].

• **Network Coverage:** Waypoints should not be placed in areas with **poor network communications**, as the calculated reassignment may not reach the truck [28].

V. Related Triggers

The reassignment waypoint is one type of event that triggers assignment re-evaluation, alongside others such as:

• A **truck detected as off-course** (Misroute Detection) [3, 7, 30, 31].

• A **shovel or dump status change** (e.g., shovel delay, Down status) [7, 12, 30].

• A **change in the loaded material** on the truck [3, 7, 30].

--------------------------------------------------------------------------------

## Mining Fleet Assignment Failure Modes

The sources extensively detail the various **Assignment Failure Modes** within the mining fleet management systems, particularly emphasizing that these failures arise when the Assignment Engine (Dynamic Programming/DA Layer) cannot determine a valid or optimal assignment due to conflicting constraints, missing data, or equipment status issues. Understanding these modes is critical because inaccurate knowledge of the mine prevents the system from making the best decisions, leading to poor assignments, queuing, idle loaders, and increased costs per ton [1, 2].

Assignment Failure Modes for Empty Trucks

When an empty truck requests an assignment to a loading tool (shovel/excavator), the assignment may fail due to the following reasons, often identified through the **Assignment Context** tool:

1. Equipment Availability and Status Issues

• **Delayed or Unavailable Machines:** The assignment fails if the intended loading tool is **on delay** [3, 4]. Specifically, loading tools that are disabled, Down, or Spare are typically removed from the LP production plan and are unavailable for automatic assignment [5-8]. If the assignment context states the loading tool is on a delay, the assignment fails, unless the dispatcher manually overrides the restriction [9].

• **Truck Unavailable:** If the truck itself is marked as unavailable for assignment, it will not receive an automatic assignment [10].

• **Loading Tool Deleted:** The loading tool may have been removed from the database [3].

• **Shovel Location/Region Issues:** The shovel may be in an **UNKNOWN location** or region, preventing it from being included in the LP solution [11-14].

2. Configuration and Compatibility Issues

• **Restrictions (Locks and Bars):** Restrictions prevent the assignment from being deemed **feasible** [15]. Failure occurs if conflicting restrictions exist, resulting in a "Dead End" scenario [3].

    ◦ An **Excessive Restriction** warning occurs if a machine is locked to only one destination [16].

    ◦ The truck may be **restricted** from the shovel due to truck-to-shovel restrictions configured via locks and bars [8, 17-20].

    ◦ **Incompatibility of Machine Classes:** The truck class may be incompatible with the loader class (e.g., due to size differences) [3, 4, 21].

• **No Path:** The assignment fails if no valid route (**Best Path**) exists between the truck's current location and the destination shovel [3, 8, 22, 23]. This can be due to problems like **steep roads**, **missed waypoints**, or if a truck is off-course and cannot connect back to the model [22, 24-26].

• **Load Time Missing:** The truck class may not have an **unload time** entered for the processor, which is checked when managing assignments [27].

• **Missing Production Plan Context:** The assignment may fail if the loading tool/processor/material is **not in the production plan** [28, 29].

Assignment Failure Modes for Loaded Trucks

When a loaded truck requires an assignment to a dumping point (processor/dump), failure modes primarily relate to material compatibility, processor status, and constraints:

1. Material and Destination Conflicts

• **Material Not Allowed at Processor:** This is a common cause where the material loaded on the truck is not explicitly configured to be accepted at the designated dumping point [4, 9, 10, 20, 30-34].

• **No Material Determination:** The Assignment Engine may not be able to determine the material the truck is hauling, which is essential for routing the truck to a compatible dump [35-38].

• **Grade Restrictions:** If the grade block loaded is restricted from the dumping point, the assignment can fail [32].

• **Mining Block Lock Conflict:** If the material from a mining block is locked to a specific destination, and that destination becomes unavailable, the assignment fails unless an alternate option is configured [20, 39, 40].

2. Processor Availability and Constraints

• **Processor Not Available:** The assigned processor is not available for assignment (e.g., status is not "Ready" or the "Avail for Assign" checkbox is unticked) [20, 23, 41, 42].

• **No Processor Unload Time:** All truck classes must have an unload time value entered for the processor in the Processor Editor [27].

• **Restrictions:** Truck-to-processor locks or bars may prevent assignments [20, 34, 40, 43].

Non-Selection Reasons (Compatible Destinations Not Chosen)

Even if a route is feasible, the optimization algorithm might choose a destination other than the one expected, which is technically a **non-selection** rather than a failure. The assignment context reveals these reasons in green text [44-46]:

• **Cost/Efficiency:** The chosen destination resulted in **smaller production loss** or had a lower cost based on calculated travel, waiting, and service costs [28, 29]. The rejected destination may have had a cycle time that was **longer or higher cost** [29].

• **Queue/Capacity:** The assignment resulted in queuing or exceeded the **dumping location queue capacity** [28, 47].

• **Scheduled Assignments:** The truck may have arrived outside the "arrive after" or "arrive before" window set for scheduled assignments [28, 48].

• **Production Goals/Blends:** The destination was not chosen because assigning the truck would violate blending requirements or production goals [28, 29, 47].

• **TKPH:** The assignment may exceed the maximum **TKPH** limits, triggering the system to restrict the truck to lower TKPH routes [16, 28, 47, 49, 50].

Consequence of Data Integrity Failures

Poor data quality is a major underlying cause of assignment issues, leading to suboptimal assignments, including [1, 2, 51]:

• **Inaccurate Time Calculations:** Manual assignments severely disrupt **DP logic** and **cycle time calculations**, resulting in **zero spot times** or **inaccurate travel times** [52, 53].

• **Misdirected Loads:** Trucks may be sent **poor assignments** or **misdirected loads** if the system lacks accurate knowledge of the mine [1, 54].

• **Operational Instability:** Poor assignments lead to operational inefficiencies such as trucks **queuing** or loaders sitting **idle** [1, 2, 54].

• **Reporting Errors:** Inaccurate data affects **production recording** and real-time **Key Performance Indicators (KPIs)**, leading to higher costs per ton [1, 2, 54].

--------------------------------------------------------------------------------

## Mining Fleet Optimization Strategy and Dynamic Assignment Logic

The sources provide a comprehensive view of the **Optimization Strategy (DOS)** (also referred to as Joptimizer in some materials), defining it as an automatic, multi-layered mechanism used by mining fleet management systems like DISPATCH and Cat MineStar System Fleet to maximize overall truck productivity, minimize operating costs, and ensure adherence to the short-term mine plan [1-5].

The DOS/Joptimizer system functions as a **real-time problem solver** that continuously receives data, updates its model of the mine, and calculates the optimal choice of assignments [6, 7].

Core Structure of the Optimization Strategy

The optimization strategy is fundamentally organized into a sequence of mathematical models that feed information to one another [4, 6, 8-10]:

1. **Best Path (BP) Model:** This preliminary layer calculates the shortest, most efficient routes (based on distance, grade, and road status) between any two points in the mine model, supplying the resulting travel times and routes to the planning layer [4, 8, 11-16].

2. **Phase 1: Linear Programming (LP) Model:** This is the strategic planning layer. It uses linear mathematical modeling to create a theoretical master production plan, known as the **LP solution** [10, 13, 17-20]. The LP solution defines optimized haulage circuits (Production Arcs) and the precise material flow rates (feed rates) required to meet production goals under defined constraints [18, 21-25].

3. **Phase 2: Dynamic Programming (DP) Model / Dynamic Assignment Layer (DA):** This is the execution layer. It takes the LP solution as its reference and enforces it in real-time by generating truck assignments instantaneously upon request from the trucks [2, 5, 10, 13, 15, 26-29]. The goal of DP is achieving "balance and synchronization" while adhering to the LP flow rates, prioritizing the least amount of lost tonnage (cost minimization) [2, 28-31].

Assignment Engine Logic and Inputs

The effectiveness of the assignment engine (DP/DA layer) relies heavily on numerous inputs and constraints fed by real-time data and system configuration. The overall objective of the logic is to **minimize each component of a haul cycle** to increase the overall number of cycles, thus moving more tonnes as cheaply as possible [2].

Key Assignment Logic Drivers (The Cost Function)

The DP model constantly recomputes the best shovel assignment for each truck, seeking the lowest cost assignment based on predicted operational outcomes [32-34]. This cost calculation (lost tons) determines the "goodness" of a potential assignment [35-38]:

1. **Time and Cost Factors:** The core cost of an assignment is derived from balancing expected **Travel Time** (multiplying forecasted travel time by a configurable cost factor, default 1.5) and **Idle Time/Queue Time** (multiplying forecasted idle time by a configurable cost factor, default 1.0) [34, 37, 39-41]. This system biases against travel time to reduce running costs [41].

2. **Shovel Priorities and Coverage:** These constraints influence the calculation of shovel need. The **Shovel Priority Factor** (SPF) is calculated based on the desired coverage and shovel priority (High=1.25, Normal=1.00, Low=0.75) [42-44]. The calculated idle cost is divided by the SPF, meaning higher priority shovels appear to have a lower cost for assignment, making them more attractive to the optimization algorithm [43].

3. **Future Scenario Forecasting:** DP builds **"future scenarios"** by predicting the Estimated Time of Arrival (ETA) for all trucks and shovels and forecasting potential queue and idle times at candidate locations [39, 45-47]. This synchronizing task allows DP to generate assignments based on an orchestrated future coverage scenario, preventing congestion (bunching) [32, 41, 46, 48].

Critical Inputs and Constraints

The assignment logic is constrained by configurations spanning the entire system [49-51]:

• **Production Plans (Goals and Priorities):** If production requirements are set (goals, priorities, rates), the assignment engine works to achieve them; otherwise, it defaults to maximizing tons moved while minimizing cost [50, 51].

• **Availability/Assignability:** Equipment must be marked as **Available for Assignment** or **Assignable** to be considered in the assignment logic [51-54]. DP removes unavailable machines from the LP production plan [53].

• **Delays:** Delays influence assignments heavily. If a duration is estimated for a delay (e.g., 15 minutes), the assignment engine may still send trucks if their predicted ETA is just as the delay is expected to end, minimizing shovel idle time [49, 55, 56]. DP also handles delays that trigger reassignments, such as when a shovel status changes [57, 58].

• **Restrictions (Locks and Bars):** These rules define how trucks are constrained to (locks) or excluded from (bars) certain shovels, dumps, or material types [51, 59-62]. DP enforces these restrictions when generating assignments (locks override restrictions) [63, 64].

• **Material Compatibility:** Dump locations must explicitly accept the material grade the truck is hauling; otherwise, the dump assignment fails [65-68].

• **TKPH Management:** Although high Tons Kilometers Per Hour (TKPH) will not cause an assignment to fail, if a warning threshold is reached, DP restricts the truck to routes associated with **shovels that have small TKPH averages** for the remaining paths, promoting tire health [49, 69-73].

• **Fuel/Special Assignments:** Scheduled assignments, such as mandatory fueling, override normal production assignments [39, 49, 74]. If a truck hits a "critical fuel level," it is assigned immediately to the nearest fuel bay, regardless of queue capacity [75-78].

• **Reassignment Waypoints and Misroutes:** Reassignment waypoints trigger re-evaluation of a truck's current assignment, especially useful when conditions change or queue capacity is reached [79-83]. Misroute detection triggers a reallocation request if a truck deviates too far from its assigned path [84-88].

In summary, the Optimization Strategy relies on synthesizing a wide array of planning, configuration, and real-time inputs (BP and LP layers) to establish a baseline of expected material movement, which the Dynamic Assignment layer then executes using a continuous, cost-minimizing logic based on forecasted outcomes [4, 5, 9, 26, 46].

--------------------------------------------------------------------------------

## Linear Programming for Mine Dispatch Optimization

The sources define **Phase 1: Linear Programming (LP)** as the critical planning stage within the overall **DISPATCH Optimization Strategy (DOS)** or **Joptimizer** system. The primary role of the LP layer is to generate a theoretical, optimal plan, known as the **LP solution** or **Production Plan**, for material movement throughout the mine by maximizing overall truck productivity while strictly adhering to numerous constraints [1-9].

1. Function and Objective of the LP Layer

The LP model's central function is to solve a complex mathematical problem designed to achieve the best outcome, typically maximizing material movement and minimizing cost [2, 4, 10-13].

• **Objective Function:** The LP layer maximizes production by minimizing a **linear objective function** that represents the hauling requirements necessary to cover dig rates in every feasible hauling route [14-16]. This means achieving the production plan with the least amount of truck cycles possible, closely aligning with cost minimization [10].

• **LP Solution / Production Plan:** The result of the LP calculation is the LP solution, which contains **optimized production circuits** (or **Production Arcs**) that specify the precise haulage paths trucks should follow and the specific **haulage flow paths and rates (feed rates)** they must achieve [2, 7, 17-22]. A production arc is defined as the unique combination of the loading tool, processor, and material [23, 24].

• **Default Behavior:** If no explicit production objectives (like Goals or Priorities) are set, the LP layer defaults to maximizing the total tons moved while minimizing cost, subject to the available equipment, assignable loading tools, model restrictions, and cycle statistics [11, 25].

2. The LP Solution Procedure (Internal Mechanism)

The LP layer systematically determines optimal feed rates by performing several complex steps [26-28]:

• **Node Properties and Initialization:** The process begins by analyzing all existing nodes (shovels and dumps) and gathering properties such as name, type, location, material lists, status, and priorities [28-30]. It sets a **Standard Truck Size** (typically the weighted average of all truck types) to calculate capacity constraints [31-34].

• **Route Creation and Feasibility:** LP determines which paths are technically feasible by checking various configurations. Feasibility ensures that only routes respecting material compatibility, grade restrictions, shovel-to-dump locks, and dump status (e.g., must be in **Ready** status) are created and considered in the solution [35-42].

• **Constraints and Capacity Building:** The LP uses continuity constraints (flow into a node must equal flow out) and capacity constraints to limit flow rates [40, 43-46]. This involves:

    ◦ **Calculating Shovel Capacity / LP Dig Rate:** This rate determines the shovel’s potential production based on Standard Truck Size, shovel dig rate, and average spot time [47-49].

    ◦ **Applying Desired Coverage:** The calculated capacity is multiplied by the **Desired Coverage Percentage** (0% to 150%) to set the required flow rate for the shovel in the LP solution [40, 50-56].

    ◦ **Dumping Constraints:** Dump capacity constraints (Nominal Rates) restrict the maximum tons per hour received, particularly crucial for crushers [43, 57-59].

• **Optimization and Weighting:** LP assigns a **"truck productivity" weight (**texttc**)** to every feasible path, favoring those that build the most productive circuits by minimizing travel times and maximizing loading rates [60-62]. These weights influence the allocation of feed rates [31, 43, 44, 63, 64].

• **Assigning Rates:** The result is the calculated **Required Haulage** (feed rate) for each Production Arc, defining the optimal flow in tons per hour (textt/h) [18, 22, 23, 31, 63, 65, 66].

3. Key Parameters and Configuration

The LP layer is highly configurable via options found in the Linear Programming Configuration Utility:

• **LP Global Parameters:** These options dictate the overall philosophy of how LP creates production circuits and feed rates [67-71]. Examples include:

    ◦ **Allow UNUSED Shovels in Undertrucked Situations (LPMATCHTRUCKS):** Controls whether LP is forced to cover all shovels (NO) or prioritizes covering high-ranking shovels at the expense of leaving low-priority shovels potentially unused (YES) in resource-scarce scenarios [7, 69, 72-84].

    ◦ **Use Global Truck Productivity Objective:** Determines whether LP prioritizes overall productivity (YES, potentially mixing truck/shovel sizes for faster hauls) or emphasizes matching truck/shovel size based on loading rates (NO) [25, 85-91].

    ◦ **Allow Truck Flows Between Ore and Waste Circuits:** If set to YES, LP maximizes flexibility by allowing haulage resources to flow between ore and waste operations (mixed circuits) [87, 92-96].

• **Priorities:** Priorities (High, Normal, Low, or 1-10 direct ranking) set a hierarchy for resource allocation, ensuring that high-priority shovels are the last to starve when capacity is limited [17, 97-104]. This ranking is crucial when **LPMATCHTRUCKS** is set to **YES** [72, 80, 105].

4. LP Recalculation Triggers

The LP solution is dynamic, requiring recalculation to remain valid against the changing mine state. The LP process runs either on a fixed **Time-based** interval (default 600 seconds or 10 minutes) or is triggered by specific **Event-based Triggers** [3, 27, 66, 106-108]:

• **Configuration Changes:** Changes to shovel production capacities, LP Global Parameters (auto-recalculates), priority settings (auto-recalculates), blending constraints, and locks/restrictions require LP to recompute the solution [66, 106, 107, 109-112].

• **Operational Changes:** Changes in equipment status (e.g., Shovel Status Change, Dump Status Change), grade or material being dug, or changes to the road network trigger LP recalculation to incorporate the new realities [3, 66, 107, 111-113].

The phrase "**Computing LP, Please Wait**" appears on the Transaction window when LP is recalculating a new solution [3, 114].

--------------------------------------------------------------------------------

## Linear Programming for Mine Truck Productivity Maximization

The sources emphasize that the overarching objective of **Phase 1: Linear Programming (LP)** within the optimization strategy (DOS or Joptimizer) is to **maximize overall truck productivity** in the mine [1-7]. This is achieved by generating a robust, theoretical master plan, known as the **LP solution**, which defines the optimal allocation of haulage resources [2, 8-10].

Core Mechanism for Maximizing Productivity

The LP model executes this goal through sophisticated mathematical processes that prioritize efficiency and flow rates:

1. **Defining the Objective Function:** The LP model's calculation begins by minimizing a **linear objective function** that represents the hauling requirements needed to cover digging rates across every feasible route [7, 11]. By minimizing this function, the LP layer ultimately maximizes the production achievable with the current fleet of trucks and shovels [7].

2. **Selecting the Most Productive Paths:** The LP model maximizes productivity by strategically choosing the **most productive circuits** from all feasible paths [8, 12]. Each feasible path between shovels and dumping points is assigned a **“truck productivity” weight (**texttc**)**, which represents how productive trucks would be if assigned to that route [12, 13].

Factors Influencing Productivity Selection in LP

The calculation of the productivity weight (texttc) and the subsequent path selection relies heavily on minimizing cycle time components, leading the LP model to favor routes with the lowest associated costs and highest throughput potential [12, 14]:

1. Optimization Based on Cycle Time Components

The formulas used to calculate truck productivity incorporate several time and size factors for both loaded and empty paths:

• **Loaded Paths (Shovel-to-Dump):** Productivity (texttc) is calculated using the LP truck size divided by the sum of the estimated travel time and dumping time [15].

• **Empty Paths (Dump-to-Shovel):** Productivity (texttc) is calculated using the LP truck size divided by the sum of the estimated travel time, spotting time, and loading time [12, 16].

These calculations result in the LP frequently choosing production circuits that have the **shortest paths (least travel times)** and the **best loading rates** [12]. LP is also mathematically inclined to **favor paths with shovels through which bigger trucks can flow** [12].

2. The Global Truck Productivity Objective Parameter

A key configurable control that dictates LP’s approach to maximizing productivity is the **Use Global Truck Productivity Objective** parameter [17-20].

• **Set to YES:** This is generally recommended because it instructs LP to prioritize **maximizing overall truck productivity** by routing haulage through the most efficient path [5, 17, 21]. This strategy emphasizes throughput, meaning that larger haulage resources (LPTRUCK Types) might be allocated to medium-sized shovels if the haul is shorter and more efficient, thus sacrificing shovel productivity for overall system productivity gain [19, 20, 22-24].

• **Set to NO:** LP emphasizes matching trucks to shovels based primarily on **truck-dependent loading rates and size** [17, 25-27]. This generally means bigger trucks go to bigger shovels and smaller trucks go to smaller shovels [17, 26-28].

3. Constraints, Priorities, and Capacities

LP must maximize productivity subject to various real-world and operational constraints [3, 4, 14, 29]:

• **Capacity Constraints:** The calculated flow rate in the LP solution must adhere to limits, such as shovel loading rates and production capacities, and dumping point capacity [3, 4, 14, 30-33]. If a crusher capacity is only 2000texttph and the shovel loading rate is 3000texttph, the feed rate in the LP solution cannot exceed the crusher capacity, thus maximizing material movement while respecting infrastructure limitations [14, 34].

• **Shovel Priorities:** In undertrucked situations, where resources are limited, LP uses priorities (High, Normal, Low) and shovel rankings to ensure key shovels receive trucks first, maximizing production from the most important areas [35-38]. LP will prioritize high-ranking shovels, allocating feed rates to cover their production capacities before lower-ranking shovels receive resources [31, 39, 40].

• **LPTRUCK Types and Dig Factors:** LP ensures high productivity by selecting the **best LPTRUCK Type** (the size/model most efficient for the digging unit) for each path based on historical dig factors. This ensures the theoretical plan maximizes the loading efficiency of the shovel [12, 13, 41, 42].

If no production objectives (like Goals or Priorities) are explicitly configured, the system defaults to simply maximizing the total tons moved while simultaneously minimizing cost [43].

--------------------------------------------------------------------------------

## Mine Production Optimization via Linear Programming

The determination of **production circuits and their associated feed rates** is the core function of **Phase 1: Linear Programming (LP)** within the overall optimization strategy (DOS or Joptimizer) used in mining fleet management systems like DISPATCH and Hexagon Mining's Jigsaw solution. The LP layer creates a **theoretical master plan (LP solution)** that dictates the optimal flow of material throughout the mine to maximize overall truck productivity.

The Linear Programming (LP) Model and its Function

The LP model is a complex mathematical programming model that creates the theoretical master plan for maximizing overall truck productivity. This plan is known as the **LP solution** [1, 2]. The primary goal of the LP layer is to maximize the production achievable with the current trucks and shovels by minimizing a **linear objective function** that represents the hauling requirements necessary to cover digging rates in every feasible hauling route [3-5].

The resulting LP solution specifies [1, 6, 7]:

1. **Optimized Production Circuits (Production Arcs):** These circuits define the paths that trucks should travel [1, 6]. A production arc is the **unique combination of the loading tool, processor, and material** [8, 9]. LP selects the paths that build the **most productive circuits** based on truck productivity weights [10].

2. **Haulage Flow Paths and Rates (Feed Rates):** The LP solution indicates the ideal **rate (feed rate)** at which the system should assign trucks to each haulage path to maintain continuity of flow [1, 5, 11-13].

The Process of Determining Production Circuits and Feed Rates

The LP solution procedure is automatically executed regularly (by default, every 600 seconds, but configurable down to 60 seconds) or when certain event-based triggers occur [14-17].

1. Creating Feasible Routes (The Circuit)

Before calculating rates, LP must identify which paths are viable, known as **feasible routes** [18-21].

• **Node Analysis:** The process starts by analyzing all existing nodes (load and dump locations) and gathering properties such as name, type, material lists, status, priority, and restrictions [22-25].

• **Material and Grade Constraints:** Material constraints ensure that shovels digging a material only feed dumps that **accept the same material type** [13, 26, 27]. If a dump does not accept the material/grade being dug, the route is **simply not created** [13].

• **Shovel-to-Dump Locks/Restrictions:** If a shovel is locked to a specific dump, a feasible route is created only to that dump [28, 29]. Similarly, if a shovel is restricted from feeding a dump, a feasible route is not created for that path [13, 29].

• **Dump Status:** For a feasible route to be created, the destination dump node must be in the **Ready** status [21, 28].

2. Defining Constraints (The Capacity Limits)

LP uses various constraints to limit the flow rates and allocate resources correctly [3, 30].

• **Continuity Constraints:** This is the most fundamental constraint, stating that the **total flow into a shovel or dump must equal the flow out** of that shovel or dump [26, 31-33]. This ensures haulage operation resembles a continuous chemical flow process in the LP solution [34].

• **Shovel Capacity Constraints:** The digging rate of each shovel serves as a constraint, specifying that the **total weight of all paths leaving a shovel must be less than or equal to the shovel's maximum dig rate** [35, 36]. Shovel capacity is calculated based on standard truck size, dig rate, and spot time [37, 38].

• **Desired Coverage:** The calculated shovel capacity is modified by the **desired coverage percentage** (which can range from 0% to 150%) [39, 40]. This directly influences the capacity requirement stored in the array of constraints [40, 41].

• **Dumping Capacity Constraints:** Capacity limits configured for dumps and crushers (Nominal Rates) restrict the **maximum tons per hour received**, ensuring the calculated feed rate does not exceed the dumping point's ability to process material [30, 41].

• **Blending Constraints:** LP enforces blending constraints to control the quality of material produced, ensuring the final material quality is within configured minimum and maximum quality parameters over a blending interval [36, 42, 43].

3. Solving and Assigning Rates

The optimization step creates the feed rates by minimizing the objective function, taking into account priority and productivity [44, 45].

• **Objective Function and Productivity:** The LP model assigns a **"truck productivity" weight** to every feasible path [46]. This weighting favors paths with the **shortest travel times** and **best loading rates**, implicitly leading LP to select production circuits that maximize throughput [10, 47].

• **Shovel Priority Factors (Coefficients):** Routes linked to shovels with higher priority (High, Normal, Low) and/or material priority are assigned a higher priority coefficient, making them **more important to the overall solution** [48, 49].

• **Feed Rate Calculation:** The required haulage rate (feed rate) for each path is calculated based on the capacity, adjusted travel time, and truck exchange time [50, 51]. This calculated feed rate determines the theoretical flow of material (in tons per hour,textt/h) that must be met by Phase 2 (DP) [6, 11, 30, 52].

In summary, the LP layer synthesizes the physical mine model, operational constraints, production goals, and equipment performance metrics to output a comprehensive set of defined production circuits, or flow plans, detailing the tonnage rates required for the system to achieve optimal efficiency [2, 12, 52-54].

--------------------------------------------------------------------------------

## Linear Programming Truck Productivity Weighting in Haulage Optimization

The concept of the Linear Programming (LP) layer selecting the **most productive paths (weighted by productivity)** is fundamental to how the optimization strategy (DOS or Joptimizer) creates the theoretical master plan for haulage operations. This selection process is critical because it dictates which production circuits the Dynamic Programming (DP) layer will attempt to enforce in real time, aiming to maximize overall truck productivity and minimize costs [1-5].

The Role of Productivity Weighting in LP Phase

The LP model operates during **Phase 1** of the optimization strategy to create an **LP solution** containing optimized production circuits, or **Production Arcs**, which define haulage flow rates [2, 3, 6, 7]. The selection of these paths is directly determined by their **truck productivity weights**:

1. **Assigning Productivity Weights:** The LP model assigns a "truck productivity" weight (texttc) to **every feasible path** between shovels (material sources) and dumping points (material destinations). These weights represent how productive trucks would be if allocated to serve those paths [8, 9].

2. **Selecting the Best Paths:** The LP model selects the paths that build the **most productive circuits** to maximize overall truck productivity. These selected paths then become the core of the Production Plan in the LP solution [10, 11].

Calculation of Truck Productivity (texttc)

The LP model estimates truck productivity (texttc) using formulas that calculate the efficiency of moving a **Standard Truck Size** along a given path [3, 8, 9, 12, 13]. These formulas prioritize attributes that minimize cycle time, implicitly favoring the most efficient routes and truck matches [10].

The productivity weighting formula differs for loaded and empty paths:

• **For Loaded Paths (Shovel-to-Dump):** Productivity is calculated based on the LP Truck Type Size divided by the sum of the truck's estimated travel time plus dumping time [9, 14, 15].texttc=fractextLPTRUCKTypeSizetextEstimatedTravelTime+textDumpingTime

• **For Empty Paths (Dump-to-Shovel):** Productivity is calculated based on the LP Truck Type Size divided by the sum of the truck's estimated travel time plus spotting time plus loading time [10, 16].texttc=fractextLPTRUCKTypeSizetextEstimatedTravelTime+textSpottingTime+textLoadingTime

These equations generally result in LP selecting production circuits that feature the **shortest paths (least travel times)** and the **best loading rates** (by implicitly choosing the best LPTRUCK Types for those paths) [10]. Furthermore, the formula causes LP to **favor paths with shovels through which bigger trucks can flow** [10].

Factors Influencing Productivity Weighting

Several critical configuration and dynamic inputs heavily influence which paths are deemed "most productive" by the LP layer:

1. **Dig Factors and LPTRUCK Types:** The LP model assigns the most productive LPTRUCK Type to a path based on historical performance, typically selecting the type that registered the highest average loading rate for a specific shovel type (LPEXCAV Type) [8, 17-20]. The best LPTRUCK Type for a shovel has a "dig factor" oftext1.0 in the system's `digfactor_tbl` table, ensuring the path is maximized for that specific truck size/type [17, 19-22].

2. **Dynamic Travel Times:** The foundation of the path's "cost" is its travel time. The system uses travel times derived from the Best Path (BP) model, which are based on distance, grade, and dynamic travel data collected from trucks traversing road segments [23, 24]. If this travel time data is corrupted or inaccurate (e.g., due to incorrect callpoint elevations), the LP model may make poor productivity judgments [25, 26].

3. **Shovel Priority and Coverage:** These constraints influence the **Priority Factor** assigned to a route, which is used to affect the coefficients of the objective function during optimization [27, 28]. Shovels with higher priorities and larger desired coverage factors are perceived as having shorter cycle times, making them more attractive options for the optimization routine [29].

4. **LP Global Parameters:** Settings like the **Use Global Truck Productivity Objective** directly impact how productivity is weighted. If set to YES, LP emphasizes maximizing _overall_ truck productivity, potentially assigning large haulage resources to medium shovels if they offer a shorter, more efficient path (maximizing flow over a perfect truck/shovel match) [26, 30-35].

Ultimately, the goal of this weighted path selection is the **maximization of overall truck productivity** while meeting operational goals and constraints defined in the LP solution [2, 5, 11, 36].

--------------------------------------------------------------------------------

## Linear Programming Constraints in Mine Material Flow

The sources extensively detail how **shovel-to-dump locks** and **material bars/restrictions** are integral constraints within **Phase 1: Linear Programming (LP)**, which calculates the theoretical master plan (LP solution) for optimizing material flow in the mine.

The LP model considers these constraints to determine the set of feasible production circuits and the optimal flow rates along those paths, which ultimately dictate real-time assignments by the Dynamic Programming (DP) layer.

Shovel-to-Dump Locks and Material Restrictions in LP

The LP model's primary function is to allocate haulage resources to maximize overall truck productivity, but it must adhere to defined constraints, including locks and restrictions [1-6].

1. Shovel-to-Dump Locks (Shovel Restrictions)

Shovel-to-dump locks constrain assignment decisions by explicitly defining the dumping location for material dug by a specific shovel [7-16].

• **Function within LP:** These locks act as constraints that force the LP solution to **only consider specific dumps as candidates for assignments** originating from that shovel [3, 10, 17-19]. If a shovel is locked to a specific dump, the LP creates a feasible route only to that dump for material loaded by that shovel [20, 21].

• **Purpose:** Dispatchers typically apply this constraint when material from a shovel **must go to a specific dump** (e.g., in-pit dumping) [7, 12].

• **Two-Way Lock Parameter:** There is a global configuration parameter, **"Treat Shovel-to-Dump Locks as Two-Way Locks,"** that affects the LP calculation [22-24].

    ◦ If set to **YES**, the lock acts as a two-way lock, meaning LP directs loaded flow to the dumped destination and empty return flow back to that same shovel [23, 25-29].

    ◦ If set to **NO** (recommended in most cases), it acts as a **one-way lock** (Shovel-to-Dump only) [23, 26, 30].

• **Overriding LP Logic:** If a shovel is locked to a dump, this rule is considered by LP when creating production circuits [3]. However, note that if a **truck is locked to a different dump**, the truck lock **supersedes** the shovel-to-dump lock in assignment decisions [7, 12, 31-34].

2. Material Bars and Restrictions

Material constraints and restrictions are vital because they ensure material dug by a shovel is directed only to dump locations that are **compatible** with that material type or grade [6, 35, 36].

• **Material Compatibility Constraints:** At a high level, LP configures paths only to dumps that explicitly **accept the material being dug** by the shovel [6, 35-37]. If a dump does not accept the material type being dug, the LP path will not be feasible, leading to potential issues where a shovel might be starved or go unused [38, 39].

• **Grade Restrictions:** LP also considers explicit **Grade Restrictions**, which restrict trucks carrying a specific grade block from being assigned to certain dumps [18, 32, 40, 41]. These are less restrictive than shovel locks, as they only apply to shovels digging that specific material block [18, 32, 40].

• **Handling Bars (Restrictions):** The core principle is that restrictions/bars prevent the creation of feasible routes in the LP solution:

    ◦ A **Dump Restriction** (Shovel Dump Restriction) supports restricting a shovel from feeding certain dumps. The route between the shovel and the restricted dump **will not be considered a feasible path** for the LP solution [19, 42].

    ◦ A **Grade Restriction** has a similar effect, restricting dumps that will not accept a specific grade block [41].

• **Impact of Ignoring Bars:** If bars are used to segregate production circuits but the global parameter **"Allow Truck Flows Between Ore and Waste Circuits"** is not set to **NO** (i.e., Mix LP is OFF), the LP calculation may diverge from real-time assignments [43-45]. Because LP does not consider these bars in its initial path calculations, it might calculate fewer trucks are needed than are actually required, causing discrepancies between the planned flow and the actual operational flow [45, 46].

Summary of Hierarchy and Effect on LP

The effectiveness of the LP solution relies on the accurate configuration of these constraints:

1. **Feasible Route Creation:** Shovel locks and material compatibility are checked during the **route creation phase** (Phase 1) [21, 47, 48]. If a dump does not accept a material, or if a shovel is locked to a non-existent dump, a feasible route is **not created** [38, 49, 50].

2. **Constraint Array:** Valid restrictions (including locks) are incorporated into the **array of constraints** used by the LP algorithm to find the optimal solution [2, 51].

3. **Hierarchy of Locks for Assignments (DP):** The highest form of lock is the **Dump Lock on Grade with Hard Lock enabled** (Hierarchy 1), followed by the **Dump Lock on Truck** (Hierarchy 2), and then the **Dump Lock on Shovel** (Hierarchy 3) [32, 52, 53]. The effectiveness of these locks determines the assignment decision made by the DP layer (Phase 2) [14, 54].

In essence, LP considers these locks and material restrictions as structural rules of the mine, using them to eliminate infeasible paths (paths where material/equipment conflicts exist) before calculating the flow rates for the paths that remain available.

--------------------------------------------------------------------------------

## Linear Programming Global Configuration Parameters for Mining Fleets

The sources identify **LP Global Configuration Parameters** as a crucial set of controls within the Linear Programming (LP) layer (Phase 1) of the optimization strategy (DOS or Joptimizer) used by mining fleet management systems like DISPATCH and Hexagon Mining's solutions. These parameters establish the fundamental rules and assumptions that guide the LP model in maximizing overall truck productivity and determining the resulting Production Plan.

The LP Global Parameters Utility screen is the interface where these overall constraints or global rules for the mine are configured. The system calculates all LP algorithms using these parameters as basic assumptions for the LP argument [1, 2].

Key Global Configuration Parameters

The sources detail several key global parameters that directly influence how LP calculates feasible production circuits and allocates haulage flow rates:

1. Allow UNUSED Shovels in Undertrucked Situations (LPMATCHTRUCKS)

This parameter is a **valuable tool for managing haulage resources in undertrucked situations** (when there are not enough trucks to cover all shovels) [3-5].

• **Set to YES:** This configuration allows **low-priority or low-productivity shovels to go unused** [3, 4, 6]. When set to YES, the LP solution determines feed rates based on constraints, available haulage resources (trucks), and shovel priority/production capacity [6, 7]. This setting enables the use of **shovel priorities, production capacities, and proportionality** to target production and fully cover key shovels, even if other shovels go unused [8-11].

• **Set to NO:** This configuration ensures that **no shovels will go unused** in undertrucked situations [3, 4, 6, 10, 12]. LP ignores priorities and proportionality because it assumes haulage resources are unlimited, allocating feed rates that theoretically cover all shovels at their maximum utilization levels [12-15]. In reality, the Dynamic Programming (DP) model then starves all shovels evenly (based on LP coverages) to match the available trucks, resulting in even coverages [15-17]. Setting this to NO is a safeguard against having shovels go unused [18, 19].

2. Use Global Truck Productivity Objective

This parameter dictates the LP model's primary strategy for matching trucks to shovels [20-22].

• **Set to YES:** LP focuses on maximizing **overall truck productivity by routing haulage through the most efficient path** [21, 23-28]. This often means that even large haulage resources (LPTRUCK Types) might be assigned to medium-sized shovels in shorter hauls, prioritizing overall throughput over a perfect size match [23, 25, 28].

• **Set to NO:** LP emphasizes matching trucks to shovels based on **truck-dependent loading rates and size** [21, 27, 29-32]. This generally results in larger trucks going to larger shovels and smaller trucks going to smaller shovels [27, 29, 32].

3. Proportionally Share Materials Dumping Capacities among Shovels

This option allows for effective management of crushing resources when capacity is limited [21, 33, 34].

• **Set to YES:** If crushing capacity is constrained, LP will **proportionally lower the feed rates of all shovels feeding that crusher** to ensure that limited processing resources are shared and **no shovel goes unused** [33-36]. This is achieved by calculating the ratio of crushing capacity to total loading capacity and reducing each shovel's feed rate proportionally [37-39].

• **Set to NO:** LP will **unuse low-priority shovels** feeding constrained crushers rather than uniformly lowering feed rates [34]. This setting is useful if the mine prefers to completely starve low-priority shovels rather than reducing throughput across all shovels [19, 33, 40].

4. Allow Truck Flows Between Ore and Waste Circuits

This option determines if trucks can be assigned across material circuits [21].

• **Set to YES (Default):** LP has the greatest flexibility to maximize truck productivity by allowing production circuits where haulage resources (trucks) flow between ore and waste operations (e.g., from an ore shovel to a crusher, then to a waste shovel, and back to the ore shovel's dump location) [21, 40-42].

• **Set to NO:** Production circuits are segregated by material type (ore circuits separate from waste circuits) [43-45]. This restricts LP's optimization capabilities but may be required if the mine needs physically separate haulage operations [43-46]. This parameter must also be set to NO if **truck bars** are used to segregate production circuits, ensuring the LP model reflects the manual assignment restrictions [41, 45, 46].

5. Treat Shovel-to-Dump Locks as Two-Way Locks

This parameter affects how manually configured shovel-to-dump locks are treated by the LP model [21].

• **Set to YES:** Shovel-to-dump locks act as **two-way locks**, meaning LP directs loaded flow to the dumped destination and empty return flow back to that specific shovel [47-49].

• **Set to NO:** Shovel-to-dump locks act as **one-way locks** (Shovel-to-Dump only) [48, 49]. Setting this to NO is generally recommended, as the YES setting is restrictive and imposes more constraints on the system [47, 48, 50].

6. Equally Divide a Shovel’s Production Rate (Dig) among its Splits

This option relates specifically to shovels digging mixed material, or "splits" [21].

• **Set to YES:** LP will **evenly distribute the shovel’s total loading rate** among its split paths in the LP solution [51-53]. This ensures LP's calculation matches the real-time scenario, maintaining computational validity [51-53]. For example, a 3000 tph shovel digging a two-way split gets 1500 tph assigned to each split path [51-53].

• **Set to NO:** LP will not evenly distribute the loading rate among the shovel's split paths [48].

7. Use Global Material Priority

This parameter enables a global material priority scheme, which is essential when using shovel priorities (High, Normal, Low) in undertrucked situations [21, 54].

• This sets the relative feeding priority between Ore and Waste materials across the entire fleet [55-57].

• Options include **Ore equals Waste** (like priorities are equivalent regardless of material type), **Ore above Waste** (ore shovels prioritized over waste shovels), or **Waste above Ore** (waste shovels prioritized over ore shovels) [55-60].

Interaction with the LP Solution

These parameters form the backbone of the **LP solution** (the theoretical master plan) [61, 62]. Any changes to shovel priorities or global parameters automatically trigger an LP recalculation [63, 64]. However, changes to other restrictions, like production capacity, require the user to explicitly select the **Force LP Recalculation** option to update the LP solution [64, 65].

The results of how these parameters affect the overall production plan can be viewed through analysis tools such as the **LP Summary Report** (which displays the status of these global parameters) and the **LP Troubleshooting Analyzer** [66-69].

--------------------------------------------------------------------------------

## Dynamic Programming in Mine Optimization Strategy

The sources describe **Phase 2: Dynamic Programming (DP)** as the real-time execution component of the overall **Optimization Strategy (DOS)** or the Joptimizer system, which works to translate the theoretical production plan (Phase 1: Linear Programming or LP solution) into concrete assignments for trucks and shovels.

I. DP within the Overall Optimization Strategy (DOS)

The DISPATCH Optimization Strategy (DOS) is a two-phase mechanism involving three mathematical models: Best Path (BP), Linear Programming (LP), and Dynamic Programming (DP) [1-9].

1. **Phase 1 (Planning):** The LP model generates the optimal haulage flow paths and rates (the **LP solution** or **master plan**) to maximize overall truck productivity based on capacity constraints, priorities, and physical mine data [2, 10-14].

2. **Phase 2 (Execution):** The DP model attempts to **enforce the LP solution in real time** by making instantaneous truck assignments that uphold the calculated feed rates and paths, while also prioritizing based on immediate field conditions [3, 7, 9, 15-20].

The DP model is frequently invoked whenever a truck requests an assignment (e.g., after dumping or loading), or when re-evaluation is necessary due to changing field conditions [13, 15, 21-23].

II. Core Objectives and Logic of DP

The primary goals of the DP layer are **"balance and synchronization"** and minimizing lost tonnage/cost by achieving the LP-determined flow rates [7, 9, 17, 22-24].

1. Generating Real-Time Assignments

DP generates assignments in response to real-time requests from trucks, ensuring that assignments are issued quickly (in a fraction of a second) so that trucks do not wait unnecessarily [22, 24, 25].

• **Assignments:** DP handles both **empty truck assignments** (to shovels) and **loaded truck assignments** (to dumping locations) [9, 17, 22, 26].

• **Neediest Shovel/Route Logic:** For empty truck assignments, DP uses a multi-step strategy: it takes a "snapshot" of the current haulage operation, identifies the "neediest" empty LP path (the one with the largest deficit between required haulage and current allocation), and then seeks the available truck that can service that path with the **least projected cost** [7, 23, 27-34].

• **Cost Calculation:** The cost function, which determines the best assignment (smallest production loss), factors in **travel time** (multiplied by a cost factor, typically 1.5) and **idle time/queue time** (multiplied by a configurable idle cost factor) [30, 35-37]. The calculation is designed to favor routes with shorter travel times [38].

2. Incorporating Constraints and Priority Factors

When making assignment decisions, DP must consider and enforce critical constraints that the LP solution may ignore, particularly real-time rules:

• **Locks and Bars:** While LP ignores most truck-to-shovel restrictions when calculating the optimal solution, **DP explicitly considers truck-to-shovel locks/bars** and other restrictions when determining if an assignment is "feasible" [17, 21, 23, 39-42]. DP also checks **material acceptance, status, and queue capacity** at dump locations [43, 44].

• **Priority Factor:** DP incorporates shovel priority and desired coverage into the assignment cost calculation via the **Shovel Priority Factor** (SPF). The idle cost associated with an assignment is divided by the SPF, meaning high-priority/high-coverage shovels appear to have a lower cost for assignment, making them more attractive to the optimization algorithm [35, 45, 46].

• **Special Assignments:** DP handles specialized assignments that override standard production logic, including **Fuel Assignments**, which are automatically generated and override DP assignments when a truck reaches a critical fuel threshold [47-51]. DP also processes reassignments triggered by **Reassignment Callpoints** or **Misroute Detection** [47, 49, 52-55].

III. Dynamic Adaptability and Future Forecasting

The DP system is continuously analyzing the operational status of the mine to maintain synchronization:

• **Snapshotting:** DP begins its decision process by taking a "snapshot" of the current haulage scenario, including the current position and expected next actions (ETA) for all equipment [27, 56].

• **Expected Assignments:** DP builds **"future scenarios"** by generating **expected assignments** for trucks that are likely to occur soon. This prevents DP from only reacting to the current need and allows it to derive assignments from an orchestrated future coverage scenario, thereby improving balance and synchronization decision by decision [57-59].

• **Reassignment Evaluation:** The DP model is responsible for re-evaluating truck assignments while trucks are enroute, particularly when they hit a reassignment waypoint or if a shovel/dump status changes, ensuring the trucks are quickly redirected to the most optimal path under the current circumstances [9, 23, 47, 54, 60-62].

In essence, DP acts as the real-time field commander, translating the strategic war plan (LP solution) into tactical orders (truck assignments), constantly adjusting for unexpected obstacles (delays, restrictions, mechanical faults) while aiming for the lowest possible operational cost and maximizing tons moved [7, 17, 34, 63].

--------------------------------------------------------------------------------

## Dynamic Programming for Real-Time Truck Assignment

The Dynamic Programming (DP) model, or Dynamic Assignment Layer, is the second critical phase of the DISPATCH Optimization Strategy (DOS) or Joptimizer process, and its core function is to **generate real-time truck assignments** in the mine by executing the theoretical master plan created by the Linear Programming (LP) layer [1-8].

Here is a detailed discussion of the DP layer's role in generating real-time truck assignments:

I. Function of DP: Executing the Master Plan in Real Time

The DP model's primary function is to enforce the optimized production circuits and flow rates defined by the preceding LP solution [2-5, 9].

• **Real-Time Execution:** DP generates truck assignments instantaneously in response to real-time requests from trucks in the field via the radio telemetry network [6, 10, 11]. This instantaneous response prevents trucks from idling unnecessarily while waiting for their next assignment [10].

• **Balance and Synchronization:** DP's fundamental strategy is one of **"balance and synchronization,"** meaning it aims to allocate available trucks to shovels and dumps to maximize overall production while strictly adhering to the calculated LP flow rates [6, 10, 12, 13].

• **Assignment Types:** DP handles two basic types of real-time assignments: **empty truck assignments** (trucks assigned to shovels) and **loaded truck assignments** (trucks assigned to dumping points) [6, 11, 14, 15].

II. Triggers for Assignment Generation

Assignments are not continuously streamed but are generated **on request** when specific events occur in the field [11, 16-20]:

• **Truck Request:** The DP model is invoked every time a truck requests an assignment [11]. For empty trucks, this occurs when the truck dumps and enters the **Traveling Empty** state [18, 21-25].

• **Load Report/Full Signal:** A loaded truck requests a dump assignment immediately after loading, typically when the shovel signals the **Full** state or the shovel operator presses OK to "load out" the truck [20, 22-28].

• **Delayed Actions:** DP generates a new assignment if a truck is detected as **off-course** [17, 22] or when a programmed **Assignment Delay ends** on the truck [29].

• **Reassignment Waypoints:** The system generates a new assignment when a truck enters a configured reassignment waypoint [29-32].

III. The Process of Generating Empty Truck Assignments

When an empty truck requests an assignment, DP executes a multi-step process focused on identifying the best truck for the neediest shovel, prioritizing those assignments that minimize projected delays and costs [12, 33, 34].

1. **Snapshot of Haulage Scenario:** DP first captures a "snapshot" of the current haulage scenario, detailing the status and location of all equipment, projected movements, and expected cycle completion times [35-38].

2. **Finding the Neediest Shovel:** DP identifies the shovel most in need of a truck based on methods such as:

• **Idle Time Method:** Prioritizing shovels (Normal or High priority) that have been idle (waiting for trucks) longer than a defined threshold (e.g., 180 seconds by default) [39-41].

• **Haulage Need Method:** Calculating the **greatest difference between the LP-required haulage** (modified by the Shovel Priority Factor) and the **current haulage** (trucks already assigned) [6, 13, 36, 42-45].

3. **Finding the Neediest Route:** After identifying the neediest shovel, DP finds the most needy route leading to that shovel (the empty LP Path with the greatest deficit between required and actual haulage) [7, 46, 47].

4. **Finding the Least Cost Truck:** DP analyzes all potential assignment options for available trucks, calculating the "cost" (lost tons) associated with each potential assignment. This calculation considers:

• **Travel Time and Cost:** Multiplying forecasted travel time by a configurable cost factor (e.g., travel time is typically 1.5 times more "expensive" than idle time) [48, 49].

• **Queue Time and Cost:** Multiplying forecasted queue time by a configurable idle cost factor [48-50].

• **Shovel Priority Factor:** Dividing the calculated idle cost by the Shovel Priority Factor (derived from priority and desired coverage) to favor high-priority routes [51-54].

5. **Executing Assignment:** The DP selects the truck that results in the smallest calculated production loss (lowest cost) for the neediest path and immediately issues the assignment to the truck's onboard display [34, 50, 53, 55-57].

IV. Constraints and Overrides

DP must respect all configured constraints and handles special assignments that override normal production logic:

• **Restrictions Check:** DP checks various truck restrictions (truck-to-shovel, material, and location) before executing the assignment [50, 57, 58].

• **Scheduled Assignments:** Mandatory assignments such as **fueling** and maintenance **override DP production assignments** [59-63]. For example, a truck hitting the critical fuel threshold is immediately assigned to a fuel bay, regardless of production needs [62, 64, 65].

• **Delay Forecasting:** If an equipment unit is on a delay with a known duration, DP is programmed to intelligently forecast the delay's end time and may assign trucks to arrive just as the equipment becomes available, thereby avoiding idle time [66-68].

V. Communication of Assignments

The final assignment is communicated to the truck operator via the onboard display as a set of instructions [69].

• **TMAC Messages:** Assignments are typically transmitted using proprietary TMAC messages, specifically through the Request / Response data flow where the application server sends the updated assignment information back to the machine [70, 71].

• **Onboard Display:** The truck operator receives the assignment instructions (e.g., "Go to E300") on the field computer and must acknowledge it, usually by pressing the truck icon or OK [72, 73].

• **Path Guidance:** Once assigned, the truck follows the "green line" (the Best Path route guidance) on its Jpanel map to the destination [74-76].

--------------------------------------------------------------------------------

## Dynamic Programming Truck-to-Shovel Restrictions and Locks

The sources provide extensive details on **truck-to-shovel locks/bars** and how they function as constraints within the optimization process, specifically influencing **Phase 2: Dynamic Programming (DP)**, which is responsible for real-time truck assignments.

The Role of Locks and Bars in DP Assignment Logic

In the context of the overall optimization strategy, the DP model takes the theoretical Production Plan (or LP solution) and executes it in real time, assigning available trucks to shovels and dumping locations to achieve the required feed rates [1], [2].

While the Linear Programming (LP) layer generally computes the optimal haulage flow paths and rates [3], [4], it is the Dynamic Programming (DP) layer that specifically enforces and adheres to **truck-to-shovel locks/bars** (which are generally ignored by the LP layer during path calculation) [5], [6], [7], [8].

The constraints established by these locks and bars directly affect the "feasibility" and "goodness" (cost minimization) calculation of potential assignments made by the DP model [9].

Types and Characteristics of Restrictions on Trucks

The generic term for these operational controls is **Restrictions**, which define rules for how trucks are to be **locked to** (restricted only to that option) or **barred from** (excluded from that option) one or more servers (shovels or processors) [10], [11].

Specific details related to truck-to-shovel restrictions enforced during the DP phase include:

• **Truck-to-Shovel Locks (Locks):** These are rules that constrain assignment decisions, limiting access **only to the item selected** [12]. A truck locked to a specific shovel will only receive loading assignments to that shovel [13], [14].

    ◦ Locks can be **Absolute** (may not be removed to permit an assignment, forcing the truck to retain its assignment even if the destination goes down) or **Provisional** (may be relaxed to permit an assignment if the destination goes on delay) [10], [15].

    ◦ The intent of locks is usually to address specific exceptions, and **excessive use of locks** can adversely affect production, leading to problems like overtrucking or lengthy queue times at shovels [11], [16], [17].

• **Truck-from-Shovel Bars (Bars):** These are rules that define exclusion, permitting access to **everything but the item selected** [12]. Barring a truck from a shovel means the DP system won't send the truck to that shovel [18], [19].

    ◦ Bars are typically used to improve operational efficiency, such as preventing small trucks from going to large shovels or excluding slow, older trucks from remote shovels deep in the pit [20], [19].

How DP Processes Truck-to-Shovel Restrictions

When the DP model is invoked (e.g., when an empty truck requests an assignment after dumping), it performs a sequence of steps, where considering restrictions is critical:

1. **Feasibility Check:** The DP checks the **Truck Restrictions** in **Phase 2: Dynamic Assignment Layer** [21]. Feasibility is determined by whether any restrictions, such as closed roads, bars from materials, or an explicit bar from the shovel itself, prevent the truck from being assigned to that shovel [9].

2. **Assignment Execution:** If a feasible route exists, the system executes the assignment for the truck with the **least cost** [21].

3. **Override Mechanism:** If a truck is **restricted** from a certain shovel, the truck-to-shovel restriction is normally honored, and the assignment will fail or be diverted to a better option [22], [23]. However, if a dispatcher manually locks the truck to that restricted shovel, **the restriction is ignored**, and the truck will be assigned for that specific manual assignment [24], [12], [25], [26].

Hierarchy and Priority of Locks

When multiple locks might apply to a dump assignment (after loading), the strength of the locks follows a specific hierarchy, noting that a **Truck Dump Lock** (a related constraint) holds considerable weight:

1. **Dump Lock on Grade with Hard Lock enabled** (highest priority, overrides all others) [27].

2. **Dump Lock on Truck** (disregards Dump Lock on Shovel and Grade Lock) [27], [28], [25].

3. **Dump Lock on Shovel, and Trucks locked to the specific shovel** [27].

The priority of having a **Dump Lock on Truck overrides all other rules**, even if the dumping location is temporarily down or on standby [29], [30]. Conversely, manually generated assignments (locks) severely **disrupt the DP logic** and cycle time calculations, creating data integrity errors such as zero spot times or inaccurate travel times [31], [32], [33]. Therefore, minimizing the use of locks and bars is advised to maintain the efficiency of the DP model [34], [8].

--------------------------------------------------------------------------------

## Mine Optimization: Production Planning and Assignment Constraints

The sources consistently emphasize that **Production Planning & Constraints** form the foundational framework for optimizing mine operations within systems like Cat MineStar System Fleet and Modular Mining's DISPATCH. This framework dictates the goals and limits the system must adhere to when generating automatic truck assignments to maximize productivity and minimize costs [1-12].

The core of Production Planning involves establishing a theoretical master plan, often referred to as the **LP Solution** or **Production Plan**, derived from various inputs and constraints [3, 8, 13-19].

1. Components of Production Planning and the Master Plan

Production Planning relies on setting clear objectives and understanding the physical and operational realities of the mine site [3, 6, 13, 20, 21].

A. Goals and Priorities (Setting Objectives)

Explicit goals and priorities define the desired outcome of the shift [1, 3, 5, 13, 22-25].

• **Production Goals/Targets:** These are numerical production objectives (e.g., tons per hour, t/h) that restrict material flow along the defined production circuits [1, 23-25]. If goals are set, the assignment engine prioritizes solutions that meet them; otherwise, it defaults to maximizing tons moved while minimizing cost [3, 22].

• **Shovel Priorities:** Priorities (High, Normal, Low, or 1-10 numerical rankings) create a hierarchy for meeting production goals and dictate the **order of capacity allocation** when resources are scarce [13, 26-34]. High-priority shovels are the last to starve in undertrucked situations [27, 35-37].

• **Coverage/Utilization Factor:** This factor dictates the maximum percentage (up to 150%) of a shovel’s loading rate that the LP model should cover in its solution, directly influencing the amount of haulage resources allocated [25, 38-44]. Setting coverage above 100% intentionally **overtrucks** a shovel to minimize idle time [25, 40, 45-47].

• **The Production Plan (LP Solution):** This is the calculated theoretical master plan based on the site's overall goals, goals, and priorities [13, 14, 16]. It consists of optimized **Production Arcs**, each being a unique combination of a loading tool, processor (dump destination), and material, along with calculated optimal flow rates (feed rates) [1, 14, 16, 19, 48, 49].

B. Haulage and Path Information (The Mine Model)

The integrity of the spatial mine model is foundational, as optimization algorithms rely on accurate data to define feasible routes and time costs [4, 50-57].

• **Best Path (BP):** The first optimization layer determines the **shortest travel routes** between any two points based on distance, grade, and road network integrity, passing this data to the LP layer [6, 50, 52-54, 58-60]. Incorrect elevation data on callpoints can lead to "wrong" assignments due to inaccurate path calculations [55, 56].

• **Dynamic Travel Times:** The system calculates travel times based on historical rolling averages, considering factors like road segment gradient, load state (empty/loaded), and truck class. These times are crucial for estimating ETA and total cycle duration, feeding into assignment cost calculations [50, 61-67].

2. Operational Constraints

Operational limitations and real-time statuses are critical constraints that modify or trigger the recalculation of the Production Plan [4, 22, 68-71].

A. Equipment Availability and Status

The status of equipment directly affects what the LP layer considers available for assignments [72-74].

• **Assignability and Availability:** For a machine (truck, shovel, or processor) to be included in the assignment calculations, it must be marked as **Available for Assignment** or **Assignable** [72, 73, 75-77]. When a machine is unavailable, it is **removed from the production plan** [72, 78].

• **Delays:** Delays (scheduled or unscheduled interruptions to productivity resulting in downtime) are a critical constraint [79, 80]. If equipment is on an "Assignment Delay," trucks will not be assigned to it [81-83]. However, the system intelligently forecasts delays (if duration is known) and may still send trucks if they are expected to arrive just as the delay ends, preventing idle time [80, 82, 84, 85].

• **Fuel Status:** Trucks running low on fuel trigger scheduled assignments to refuel, overriding normal production assignments when fuel levels reach critical thresholds [22, 68, 86-91].

B. Material and Resource Constraints

Material characteristics, blending needs, and physical throughput limits restrict which production arcs are feasible [71, 74, 92].

• **Material Compatibility:** The system ensures that material being dug by a shovel is accepted by the assigned dumping location (processor) [93-95]. Incompatible materials prevent the creation of feasible routes [94, 96].

• **Blending Requirements:** Joptimizer enforces **blending constraints** to control the quality and composition of material delivered to dumps/crushers based on defined quality parameters and control intervals (mass or time) [22, 39, 68, 97-103].

• **Capacity Constraints (Digging and Dumping):** Both shovels and processors have capacity limits. The total material flow leaving a shovel must be less than or equal to its maximum dig rate [94, 104]. For dumps (especially crushers), capacity constraints or nominal rates limit the maximum tons per hour received, diverting excess material elsewhere [63, 97, 98, 105-108].

• **Queue Capacity:** Dump and shovel locations can be configured with a maximum queue capacity (number of trucks). If this limit is exceeded, trucks will be reassigned to alternative locations, mitigating excessive queuing [46, 105, 109-114].

C. Operational Restrictions (Locks and Bars)

User-defined constraints override normal optimization logic to enforce specific operational demands [26, 68, 115-121].

• **Restrictions (General):** These are rules (Locks and Bars) that define how trucks are constrained to, or excluded from, specific locations (shovels, dumps, regions) or materials [26, 115, 116, 118, 119, 121]. They address exceptions to the model, although their excessive use can lead to overtrucking, undertrucking, and queuing [4, 118].

• **Lock Hierarchy:** Locks override restrictions. The hierarchy ensures that the **Dump Lock on Grade with Hard Lock enabled** is the highest priority lock, overriding all others, followed by Dump Lock on Truck, then Shovel Dump Lock, and finally Grade Dump Lock [80, 122-124]. Manual assignments override all programmed assignments and locks [125-128].

• **TKPH Constraints:** Though a high Tons Kilometers Per Hour (TKPH) reading does not stop an assignment, it triggers the optimization engine to steer the truck towards alternate routes (shorter hauls or low-TKPH routes) to limit tire wear [22, 68, 129-135].

3. Impact on Operations and Assignments

The goal of coordinating all these planning factors and constraints is the automated generation of optimal **Assignments** by the Dynamic Assignment (DA) layer [2, 15, 136-139].

• **DA Execution:** The Dynamic Assignment (DP/DA) layer continuously executes the theoretical Production Plan (LP Solution) in real time by managing truck requests, prioritizing the neediest shovel, and calculating the lowest cost assignment for the available trucks, striving for "balance and synchronization" [2, 15, 17, 65, 137-143].

• **LP Recalculation:** The LP solution is automatically recalculated (by default, every 600 seconds/10 minutes) or triggered by specific event-based changes, such as changes in shovel status, priority, coverage, or road network configuration, to ensure the production plan remains optimal for the current mine state [20, 49, 144-148].

• **Consequences of Poor Data:** Without accurate mine knowledge (e.g., proper configuration, accurate shovel statuses, correctly maintained roads), the system cannot make the best assignment decisions, potentially resulting in queuing, idle loaders, misdirected loads, and reduced productivity, leading to higher costs per ton [4, 149-157].

--------------------------------------------------------------------------------

## Production Goals and Constraints in Automated Mining

The sources extensively define **Goals** as explicit, quantifiable targets crucial for guiding the automated **Production Plan** and optimizing mining operations within the complex landscape of Production Planning & Constraints. Goals serve as the definitive measures against which the assignment engine operates, determining resource allocation and material flow.

Goals as Numerical Production Objectives

**Goals** are fundamental concepts in production planning, representing the numerical objectives that must be achieved during the course of the shift [1, 2]. They are explicitly defined as **numerical production objectives** and their function is to **restrict the material flow along the production arcs** [1, 3].

In systems like Cat MineStar Fleet/DISPATCH, if production requirements or goals are set, the assignment engine prioritizes assignments to meet these numerical objectives [4]. If no explicit production objectives are set, the system defaults to maximizing tons moved while minimizing cost, given the current mine model state [4].

Configuration and Structure of Goals (Constraints)

Goals function as constraints established by management to drive the optimization process toward specific output mandates:

• **Definition via the Goal Editor:** Goals are configured using a **Goal Editor**, which allows users to specify both minimum and maximum production requirements [5-7]. These quantitative constraints define the volume of material flow (in tons per hour,textt/h) [6].

• **Key Components:** When creating a goal, users define the specific parameters of the desired production flow, including:

    ◦ **"At least"**: The minimum required amount of material flow (intextt/h) [6, 7].

    ◦ **"No more than"**: The maximum allowable amount of material flow (intextt/h) [6, 7].

    ◦ **"From"**: Specifies the loading tool (e.g., shovel/excavator) that is the source of the material [6, 7].

    ◦ **"Material"**: Specifies the material or blend type involved in the production flow [6, 7].

    ◦ **"To"**: Specifies the processor (dump, crusher, or stockpile) that is the destination for the material [6, 7].

• **Location of Setup:** These objectives are set via the **Production Requirements Assistant** or Editor, a crucial utility used by Controllers to manage the production objectives that drive the assignment engine [8, 9].

Goals and Optimization Behavior

The existence and configuration of Goals profoundly impact the behavior of the assignment algorithms:

• **Enforcement:** Goals are hard requirements in the system; if the assignment logic calculates that a particular assignment would cause the **production goals will not be met**, that assignment may be rejected [10].

• **Hierarchy for Optimization:** While **Priorities** set a hierarchy for meeting goals and determine the order of capacity allocation, **Goals** themselves provide a definitive value for the system to work toward [11, 12]. This direct articulation of objectives makes them preferable to the ambiguity of priorities when defining production targets [12].

• **Blending Compliance:** Goals are closely linked to blending management. The system enforces blending constraints to control the quality of material delivered to dumps/crushers, which aligns with meeting production requirements [13-15]. If blend targets cannot be met, the assignment may fail [16].

Best Practices and Flexibility (Constraints Management)

Effective production planning relies on setting realistic and practical constraints through Goals:

• **Recommended Buffer:** It is considered **best practice** to implement a **10% buffer for all numerical targets** set in the Goal Editor [12]. This crucial buffer provides the assignment engine with the flexibility needed to **meet the target and exceed it under some circumstances** [12].

• **Avoiding Over-Constraining:** Goals should **not be set too wide or too tight** [12]. Setting goals too narrowly can unnecessarily restrict the assignment algorithm, potentially hindering efficient assignment decisions and causing operational issues such as excessive queuing [12].

• **Monitoring Conformance:** The setting of Goals defines the target, but adherence is continuously monitored. Tools like **Decision Support** allow users to evaluate the impact of proposed changes to Goals before implementing them live, helping ensure that the established constraints actually lead to the desired production outcomes [5, 17, 18].

--------------------------------------------------------------------------------

## Defining and Optimizing Production Goals in Fleet Management

The sources define **Goals** as **numerical production objectives** that are critical constraints used by the fleet management system's optimization engine (Linear Programming or LP layer) to guide the Production Plan and achieve site targets. These numerical values provide explicit quantitative boundaries for material flow and resource allocation during a shift.

Definition and Purpose of Numerical Production Objectives

**Goals** are explicitly defined as the **numerical production objectives to be achieved during the course of the shift** [1, 2]. They represent the site's desired outputs, translating the overall mine plan into quantifiable requirements [3-5].

The central purpose of these numerical goals is to serve as restrictions on the **material flow along the production arcs** [4, 6, 7]. The Assignment algorithm then uses these numerical inputs to **allocate capacity at the mine** effectively [1, 8].

Setting Numerical Targets in the Goal Editor

The implementation of these numerical production objectives occurs via the **Goal Editor**, accessible through tools like the **Production Requirements Assistant** [9, 10]. This editor allows controllers to specify precise flow rates for particular material circuits:

• **Minimum Target ("At least"):** Users enter the amount of material (intextt/h) that represents the minimum requirement for the goal [9, 11, 12]. The system attempts to prevent production from falling below this minimum [9].

• **Maximum Target ("No more than"):** Users specify the maximum amount of material (intextt/h) allowed for the goal [9, 11, 12]. This value acts as an upper constraint on the material flow rate [9].

• **Defining the Flow:** The goal explicitly connects the source (the loading tool, specified in the **"From"** field), the material type (in the **"Material"** field), and the destination (the processor/dump, in the **"To"** field) [11, 12].

Importance and Configuration in Optimization

When production requirements (including goals) are configured in the system, the Assignment engine prioritizes assignments to meet these **numerical production objectives** [8].

1. Enforcement as Constraints

The numerical goals serve as hard limits. If a calculated assignment suggests that the **production goals will not be met**, that assignment may be rejected by the system [13]. These goals are crucial elements considered when the optimization algorithm determines which assignment option is best for a truck [14].

2. Best Practice: Using Buffers

To ensure flexibility and prevent the algorithms from being overly constrained, a critical recommendation is to apply a **10% buffer for all numerical targets** [2].

• This buffer allows the Assignment engine **flexibility to meet the target and exceed it under some circumstances** [2].

• The system advises against setting the minimum ("At least") and maximum ("No more than") goals too **"tight"** (narrowly separated) or too **"wide"** (too easily met), as tightly set constraints can interfere with efficient assignment decisions [2].

3. Goals Preferred Over Priorities

In the context of setting production requirements, utilizing numerical **Goals is considered best practice over priorities alone** [2]. Priorities are seen as **ambiguous**, and reliance on them has, in some cases, led to inefficient assignment decisions and **excessive queuing** [2]. Goals offer a **definitive value to work towards**, providing clarity for the optimization routines [2].

Operational Use and Verification

Before goals are implemented, they can be tested using features like **Decision Support**, which allows the user to validate the proposed goals against the production plan before activating them live in the system [9, 11, 15]. Additionally, the actual impact of goals is monitored throughout the shift to confirm that the assigned objectives are being met effectively [16].

--------------------------------------------------------------------------------

## Defining Production Goals in Mine Management Systems

The **Production Requirements Assistant** is the central utility used in the Cat MineStar System (and functionally similar tools in Jigsaw/Hexagon Mining systems) that allows mine controllers to define and manage the parameters that govern the overall **Production Plan**. Within this Assistant, **Goals** are explicitly created and defined, serving as the quantitative objectives that drive the assignment engine's optimization logic.

Function of the Production Requirements Assistant

The **Production Requirements Assistant** is the designated page that enables the user (typically the Controller) to **view, edit, and create production requirements** that ultimately drive the overall assignment engine to achieve the site's goals [1].

The main component for setting these objectives within this utility is the **Production Requirements Editor**, which is typically opened from the Assistant [1]. This editor is where new goals and priorities are entered and adjusted [1].

Defining Goals via the Goal Editor

When establishing new requirements within the Production Requirements Assistant, the user interacts with the **Goal Editor** to define the specific production objectives [2, 3].

The Goal Editor requires and allows users to input specific parameters that define a target material flow (tons per hour,textt/h) across a specific production circuit:

• **At least:** This field is used to enter the minimum amount of material (in tons per hour,textt/h) that is required for the goal [2-4].

• **No more than:** This field specifies the maximum amount of material (in tons per hour,textt/h) that is allowed for the goal [2-4].

• **From:** This identifies the specific **loader** (shovel) that is the source of the material flow related to the goal [2, 3, 5].

• **Material:** This names the specific **material or blend of material** that the goal pertains to [2, 3, 5].

• **To:** This specifies the **processor** (dump destination) for the material flow of the goal [2, 3, 5].

These inputs define quantifiable targets that the assignment engine's mathematical models must attempt to satisfy [6, 7].

Best Practice for Setting Goals

The process of setting goals within the Production Requirements Assistant is crucial, and the sources recommend specific practices to ensure system efficiency:

1. **Goals Preferred over Priorities:** Using Goals is considered **best practice for setting a goal** because **Goals provide a definitive value to work towards**, while Priorities are inherently ambiguous [2]. The use of ambiguous priorities has been suggested to cause inefficient assignment decisions, potentially leading to excessive queuing [2].

2. **Using Buffers:** When specifying numerical targets ("At least" and "No more than"), a **10% buffer is recommended for all numerical targets** [2]. This buffer provides the assignment engine with the necessary flexibility to **meet the target and exceed it under some circumstances** [2].

3. **Timing Constraints:** The defined minimum ("At least") and maximum ("No more than") goals should **not be set too wide or too tight** [2]. If a goal involves a scheduled assignment, the assignment can fail if the truck is projected to arrive outside the specified window (e.g., "arrive after" or "arrive before") [8].

4. **Single User Editing:** Only **one Controller should be adjusting production goals at one time** because the last saved plan will overwrite any previous changes made by another user [2, 9].

Once goals are defined and validated in the editor, they are incorporated into the Production Requirements Editor and become active, directly governing the optimization strategy of the assignment engine [2].

--------------------------------------------------------------------------------

## Mine Fleet Goal Editor and Production Planning

The **Goal Editor** is a specific interface used within the mining fleet management system (like DISPATCH or Joptimizer) to define quantitative constraints and objectives, or **Goals**, which directly influence the system's Production Plan and truck assignment logic. This tool allows controllers to articulate precise production requirements for material flow within the mine [1-3].

The Goal Editor explicitly provides fields corresponding to the components that define a production objective: **At Least**, **No More Than**, **From**, **Material**, and **To** [1, 3, 4].

Components and Functionality of the Goal Editor

The Goal Editor allows users to define production requirements in terms of tonnage rate (textt/h) and the specific circuit involved in moving that tonnage [3, 4]:

1. **At Least:** This field allows the user to enter the **minimum amount of material** (in tons per hour,textt/h) that is required to meet the production objective [3, 4]. The system attempts to ensure that production does not fall below this minimum target [2].

2. **No More Than:** This field allows the user to enter the **maximum amount of material** (in tons per hour,textt/h) allowed for the goal [3, 4]. This maximum value serves as a cap on the material flow rate specified by the goal [2].

3. **From:** This field allows the user to specify the **loader** (shove/excavator) that is the source of the material flow for the goal [3, 4]. This is critical as the loading tool's capacity forms a fundamental constraint in the Production Plan [5].

4. **Material:** This field allows the user to enter the **material or blend of material** that the goal pertains to [3, 4]. This directly relates to the material tracking and quality control aspects of the mining operation [6, 7].

5. **To:** This field allows the user to enter the **processor** (dump, crusher, or stockpile) that is the destination for the material flow defined by the goal [3, 4].

Goals in the Context of the Production Plan

The numerical objectives configured through the Goal Editor are directly applied to the Production Plan by the optimization layer (LP) and serve multiple strategic purposes:

• **Defining Constraints:** The defined goals act as **hard constraints** on the LP solution, restricting the material flow along specific production arcs [2, 8]. The Assignment algorithm uses these goals to allocate capacity at the mine effectively [9].

• **Controlling Assignments:** If the system determines that a truck assignment would result in the production goals **not being met**, that assignment may be rejected [10].

• **Management Preference (Best Practice):** Defining objectives using Goals is considered **best practice over using priorities** alone, because **Goals provide a definitive value to work towards**, whereas priorities are inherently ambiguous [2]. Ambiguous priorities can sometimes lead to inefficient assignment decisions, such as excessive queuing [2].

Recommendations for Using the Goal Editor

To ensure the system maintains flexibility while attempting to meet the production targets set via the Goal Editor:

• **Buffer Recommendation:** It is recommended to use a **10% buffer** for all numerical targets set in the Goal Editor. This buffer allows the Assignment engine the necessary flexibility to effectively **meet the target and exceed it under some circumstances** [2].

• **Balance of Values:** The minimum ("At least") and maximum ("No more than") values defining the goals should **not be set too wide or too tight** [2].

Location within the System

The Goal Editor is accessed via the **Production Requirements Assistant** within the overall system utility. Once goals are defined in the editor, they are incorporated into the Production Requirements Editor, which holds the current set of production objectives for the shift [11, 12]. The effect of these goals can be simulated and checked using the **Decision Support** functionality before activation [1, 13].

--------------------------------------------------------------------------------

Optimal Production Goal Setting with a 10% Buffer

The sources strongly recommend applying a **10% buffer** for **numerical targets** within the context of **Goals** for the production plan. This recommendation is considered **best practice** because it provides the automated system with the necessary flexibility to achieve and potentially exceed production targets without leading to restrictive inefficiencies.

Here is a detailed discussion of this recommendation in the context of production Goals:

Context of Numerical Targets (Goals)

Goals are integral to the Production Plan because they are defined as **numerical production objectives** that the mine aims to achieve during a shift, often expressed in tones per hour (textt/h) [1, 2]. These targets form constraints that dictate how the assignment engine should allocate haulage capacity [1, 3].

Goals typically specify both minimum and maximum production requirements, using "**At least**" and "**No more than**" values in the Goal Editor [4, 5].

The Recommended 10% Buffer

The primary recommendation for setting these numerical goals is to introduce a **10% buffer** for all numerical targets [5].

• **Purpose of the Buffer:** This buffer is designed to **allow the Assignment engine flexibility to meet the target** and potentially **exceed it under some circumstances** [5].

• **Application in Blending:** A similar principle applies to blending criteria, where setting the Material Blend Criteria using ratios results in a range of 22.5 to 32.5, providing a 10 **range width**, which is recommended "in order to not overly constrain Assignment" [6].

Consequences of Improper Goal Setting

If numerical targets (Goals) are defined without sufficient flexibility (i.e., if the targets are set **too tight**), they can lead to operational issues:

• **Constraint Violation:** If the timing constraints are too restrictive (e.g., an assignment is required outside the defined "arrive after" or "arrive before" window), the assignment will be rejected [7].

• **Ambiguity and Inefficiency:** Goals are generally seen as preferable to using only **Priorities**, because **Priorities are ambiguous**, and relying solely on them has, in some observed instances, caused **inefficient assignment decisions leading to excessive queuing** [5]. However, even clear goals can create problems if they are set too narrowly; the recommendation advises against setting goals that are **too tight** [5].

The general principle is that the system operates most effectively when provided with clear, achievable bounds that align with production requirements (Goals) while retaining enough operational margin (the 10% buffer) to dynamically maximize efficiency across the entire haulage fleet without triggering restrictive assignment failures [1, 8].

--------------------------------------------------------------------------------

## Production Planning Priorities and Shovel Allocation Schemes

The sources extensively discuss **Priorities** within the context of **Production Planning & Constraints**, defining them as critical operational controls used primarily in optimization models (like DISPATCH's LP/DP or Hexagon's Joptimizer) to direct resource allocation and ensure high-value production targets are met, especially when resources (like trucks) are limited.

Role of Priorities in Production Planning

The overall **Production Plan** is automatically computed based on the **site’s overall goals, as set in the system through priorities and goals** [1]. Priorities establish a hierarchy that governs the allocation of haulage capacity and determines which equipment receives trucks first [1-7].

Priorities are fundamentally necessary when the system encounters **undertrucked situations** (not enough trucks to cover all demand), forcing shovels to compete for limited haulage resources [8-15].

I. Shovel Priority Schemes and Levels

The most common application of priorities is defining shovel ranking, which dictates the order of capacity allocation [2, 4, 16].

Shovel priorities can be set using different schemes [2, 4, 17]:

1. **Global Priority Scheme (1 through 6):** This scheme sets priorities based on material type (Ore, Waste) using descriptive rankings: **High, Normal, or Low** [4-8, 14, 17-36].

• This scheme converts the High, Normal, Low setting into a definitive priority level between 1 (highest) and 6 (lowest), factoring in the **Global Material Priority scheme** (e.g., Ore Above Waste or Ore Equals Waste) [17, 29-32, 37-43].

• For example, under the _Ore Above Waste_ scheme, priorities are strictly ordered: High Ore (1) > Normal Ore (2) > Low Ore (3) > High Waste (4) > Normal Waste (5) > Low Waste (6) [30, 38, 44].

2. **Direct Priority Ranking (1 through 10):** This scheme allows dispatchers to directly set a numerical priority level from **1 (highest)** to **10 (lowest)** for each shovel, bypassing the material-based scheme [2, 4, 11, 17, 26, 45-49]. Dispatchers may intentionally skip numbers in the scale to allow for **"tweaking"** during a shift without re-ranking all shovels [46, 48].

II. Priority Application and Allocation

When the LP model computes the production circuits (Production Arcs) and feed rates (the Production Plan), priorities guide the sequence of resource fulfillment [50-52]:

• **Order of Allocation:** LP allocates feed rates in the solution starting with the **highest ranking shovel** (Priority 1) first. The **lowest ranking shovels** receive remaining haulage resources last and may consequently **starve** (receive insufficient trucks) or go **unused** [9, 10, 13, 15, 16, 23, 30, 50-60].

• **Tie-Breaker:** If multiple shovels share the same priority level, the system breaks the tie by ranking the shovel that has the **highest truck productivity** (generally based on the shortest and most efficient loaded path) higher [11, 17, 48, 50, 61-65].

• **Dynamic Assignment (DP) Enforcement:** The DP layer enforces the plan by prioritizing assignments to the shovel path with the **highest ranking shovel** when choosing between competing assignment options [43, 45].

III. Priorities vs. Goals: Ambiguity and Impact

While priorities are used to establish a hierarchy, the sources highlight that **Goals** (numerical targets) are generally the **best practice** for setting production objectives because **Priorities are ambiguous** [66].

• **Ambiguity Leading to Problems:** The ambiguity of priorities has reportedly **caused inefficient assignment decisions leading to excessive queuing** [66].

• **Goal Function:** Goals provide a definite **numerical value** to work towards, placing explicit "At least" (minimum) and "No more than" (maximum) constraints on material flow, making the objective clearer for the optimizer [66-71].

• **Priority Factor Calculation:** Priorities influence the optimization by acting as a multiplier. For example, a shovel's priority is converted into a factor (e.g., High=1.25, Normal=1.00, Low=0.75) which is incorporated into the calculation of the **Shovel Priority Factor** that determines haulage needs [35, 72-76].

IV. Priority Settings as Optimization Constraints

Priorities are fundamentally integrated with other constraints in Production Planning [77-80]:

• **Shovel Coverage (%):** Priorities work alongside the **Desired Coverage Percentage** (0% to 150%) [27, 28, 34, 35, 42, 59, 73, 76, 81-93]. A higher priority shovel may have its desired coverage intentionally set above 100% (e.g., 150%) to **overtruck** it and ensure minimal idle time, making it highly favored in the allocation order [28, 34, 81, 83, 87, 90-92, 94-97].

• **Proportional Feeding:** In undertrucked situations, **Proportional Feeding** (a configuration option) can override strict priority rankings among low-ranking shovels to ensure they receive _some_ resources, preventing them from being completely starved [15, 98-121].

• **Crusher Competition:** Priorities are necessary to resolve resource competition, primarily when multiple shovels feed a capacity-constrained crusher [3, 54, 99, 106, 109, 115, 116, 122-130]. If the "Proportionally Share Materials Dumping Capacities among Shovels" option is set to **NO**, LP will intentionally starve low-priority shovels feeding the constrained crusher rather than lowering feed rates across all competing shovels [116, 127, 130-132].

--------------------------------------------------------------------------------

## Optimization Hierarchy: Goals, Priorities, and Constraints

The sources indicate that there is a **hierarchy of goals and priorities** that the optimization system (specifically the LP and DP models) must manage when generating the Production Plan and determining truck assignments. This hierarchy establishes the relative importance of different operational targets and constraints, especially in resource-constrained situations like under-trucking.

While both **Goals** (numerical targets) and **Priorities** (rankings) are fundamental inputs, the sources strongly suggest that **Goals are preferable to Priorities** for achieving clear production targets because Priorities are inherently ambiguous.

I. The Purpose and Function of Priorities

Priorities are fundamentally designed to create a structure for resource allocation, particularly for shovels and materials, when the system cannot meet every demand simultaneously:

• **Order of Capacity Allocation:** Priorities dictate the **order of capacity allocation** to equipment [1-5]. In under-trucked situations (where there are insufficient trucks to cover all shovels), the system must compete for limited haulage resources [3, 6-9].

• **LP Ranking:** The optimization layer (LP) uses the priority settings to establish a **shovel ranking hierarchy** [10-14]. Shovels with the highest priority level (Level 1) are the first to receive haulage feed rates in the LP solution [10, 14, 15]. Conversely, lower-priority shovels are the first to starve or potentially go unused [7, 8, 16-20].

• **Types of Priority Schemes:** The system supports various priority schemes, including the **Global Priority Scheme** (1-6, often based on material types like Ore Above Waste) and the **Direct Priority Ranking** (1-10) [1, 5, 21-25].

II. The Ambiguity of Priorities vs. Clarity of Goals

The sources explicitly caution against relying solely on prioritization because of its potential for ambiguity, favoring the use of precise numerical goals instead:

• **Goal Preference (Best Practice):** It is recommended that **Goals should be used over priorities** for setting objectives because **Goals provide a definitive value to work towards** [26].

• **Ambiguity of Priorities:** Priorities are described as **ambiguous** [26]. This ambiguity can lead to operational inefficiency, as suggested by evidence that the use of priorities has sometimes **caused inefficient assignment decisions leading to excessive queuing** [26].

• **Goal Definition:** Goals are clear, numerical production objectives (e.g., in tons per hour, t/h) that restrict material flow along production arcs [27-30]. They set specific "At least" (minimum) and "No more than" (maximum) production requirements [28, 31].

When managing the production plan, Goals enable greater control by giving the assignment engine specific compliance metrics, whereas priorities merely provide a hierarchical preference for resource allocation in contested scenarios [26, 27].

III. The Integrated Hierarchy of Optimization Factors

In the broader context of the assignment engine logic, Priorities and Goals are two major inputs alongside other constraints that form the overall hierarchy used by the system when making decisions [32-36].

When deciding where to send a truck, the Dynamic Programming (DP) layer considers a strict order of preference, encompassing both manually set controls and dynamic calculation results:

1. **Hard Locks and Constraints:** Manual assignments, such as **Dump Lock on Grade with Hard Lock enabled**, supersede all other locks and force the truck to tip at the designated dump location [37-39]. Similarly, an explicit **Manual Assignment** will override any programmed Locks or Joptimized assignments [35, 38, 39].

2. **Special Assignments:** Mandatory requirements like **Scheduled Assignments** (including **refueling**) and **TKPH constraints** are treated as critical “Other Considerations” that influence assignment outcomes and override standard DP assignments [34, 36]. For example, a truck needing fuel might be immediately directed to a fuel bay despite its productivity score [34, 40, 41].

3. **Production Goals/Optimization:** The core optimization then proceeds based on the Production Plan, utilizing numerical **Goals** and the priority-based ranking [1, 30, 33, 36]. This includes adherence to production goals and minimizing cost based on computed haulage rates and material priorities [33, 42].

Thus, while priorities establish the order of allocation when resources are scarce (e.g., highest priority shovels starve last), objectives set via definitive **Goals** provide the clearest operational boundaries for the automated system to follow [26].

This layered system of controls ensures that mandatory tasks (like safety and maintenance, represented by scheduled assignments and TKPH constraints) and fundamental business objectives (production goals and required material flow) are met before the system defaults to simply prioritizing the fastest, highest-ranked operations.

--------------------------------------------------------------------------------

## Mine Fleet Priority and Capacity Allocation Hierarchy

The concept of **Priorities** is directly linked to the **order of capacity allocation** for equipment, especially shovels, within the optimization strategies of mining fleet management systems like DISPATCH and Joptimizer. Priorities dictate which resources receive limited hauling capacity first, particularly when the mine is facing an undersupply of trucks (undertrucked situations).

Here is a detailed discussion of what the sources say about the order of capacity allocation in the context of Priorities.

I. Priorities Define the Allocation Hierarchy

Priorities are mechanisms used to **create a hierarchy for the goals that are met** and determine the **order of capacity allocation** [1, 2].

In situations where there are insufficient haulage resources (trucks) to cover all operating shovels at their maximum production levels (an **undertrucked situation**), the Linear Programming (LP) model uses this defined priority structure to decide which shovels are serviced first [3-6].

Key facts about this allocation order:

• **Highest Priority First:** The highest priority shovels (typically assigned a ranking of 1) are the first to receive haulage feed rates in the LP solution [3, 7-12].

• **Lowest Priority Starved First:** Conversely, **Low priority shovels are the first to starve** or potentially go unused in undertrucked situations [3, 4, 13-20].

• **Dynamic Programming (DP) Enforcement:** The Dynamic Programming (DP) model executes the LP solution in real time, enforcing the calculated feed rates and ensuring that trucks are assigned according to this priority-based allocation order [21].

II. Priority Schemes and Levels

The systems utilize different schemes to set and enforce this allocation order:

1. **Global Priority Scheme (1 through 6):** This scheme sets shovel priority based on the material type being dug (e.g., Ore above Waste, Waste above Ore, or Ore equals Waste) [4, 22-30].

• This scheme assigns a **priority level from 1 (highest) through 6 (lowest)** to each shovel, based on the global material priority setting and the shovel’s individual material priority (High, Normal, Low) [22, 31, 32].

• If the global priority is set to **Ore above Waste**, the hierarchy places all ore priorities (High, Normal, Low) above all waste priorities, meaning Low ore is still ranked higher than High waste [16, 33].

2. **Direct Priority Ranking (1 through 10):** This scheme allows the dispatcher to directly set a priority level (1 to 10) for each shovel, bypassing the global material scheme [22, 28, 34-38]. This approach provides **simpler and more flexible** control for direct ranking [37, 38].

III. Tie-Breaking and Productivity

When multiple loading units share the same priority level, DISPATCH employs a mechanism to break the tie and further refine the allocation order:

• **Most Productive Loaded Path:** The tie is broken by calculating the **most productive loaded path** [39]. Generally, this favors the **shortest path** [39].

• **Highest Truck Productivity:** The LP model ranks higher the shovel that has the **highest truck productivity** [7, 11, 35, 40, 41]. Truck productivity is measured based on a ratio involving LP Truck Type size, estimated travel time, spotting time, and loading time [42-46].

• **LP Shovel Ranking Report:** Dispatchers can view this ranking hierarchy and the relative truck productivity of each shovel using the **LP Shovel Ranking Report** [47-51].

IV. Interacting Controls Affecting Allocation

The priority setting heavily influences how other control mechanisms affect capacity allocation:

• **Shovel Coverage (% UTILIZATION):** Priorities work in conjunction with the **Desired Coverage Factor** (or maximum utilization level, set from 0% to 150%) to calculate the haulage flow rate necessary for each shovel [49, 52-57]. Raising the priority and/or the desired coverage percentage increases the calculated capacity, resulting in **higher required haulage** and a higher chance of truck assignments [58-60].

• **Proportional Feeding:** This specialized tactic allows the dispatcher to intentionally distribute remaining haulage resources proportionally among lower-ranking shovels after the higher-ranking shovels are covered [10, 18, 61-64]. This tactic is primarily used in undertrucked situations and prevents low-priority shovels from going entirely unused [18, 61, 63-67].

• **Crusher Competition:** Priorities are used to favor one shovel over another when two or more shovels are **competing for constrained crusher resources** [14, 68-71]. For example, adjusting the coverage percentage on a larger capacity shovel can allow smaller shovels to receive assignments, particularly when the mine is undertrucked [72].

--------------------------------------------------------------------------------

## Goals Versus Priorities in Production Planning

The sources provide a direct recommendation regarding the use of **Goals** over **Priorities** within the Production Plan, suggesting that Goals offer a less ambiguous method for guiding the assignment engine.

Goals vs. Priorities in Production Planning

In the context of the overall Production Plan and constraint management, both Goals and Priorities are mechanisms used to influence the assignment engine's behavior, particularly in allocating truck capacity to various shovels and dumps, thereby steering material flow.

The key distinction and recommendation are:

• **Goals Preferred for Clarity:** It is considered **best practice for setting a goal to use goals over priorities** because **Goals provide a definitive value to work towards** [1].

• **Ambiguity of Priorities:** Conversely, **Priorities are ambiguous** [1].

Detailed Comparison of Goals and Priorities

When production requirements are established in the system, the assignment engine prioritizes solutions that meet these quantitative objectives [2]. Both mechanisms contribute to this process, but in different ways:

1. Goals (Numerical Objectives)

Goals are specific **numerical production objectives** that must be achieved during the course of the shift [3], [1].

• **Definitive Values:** Goals offer explicit upper and lower constraints, such as targets to produce **"At least"** a minimum tonnage per hour (textt/h) and **"No more than"** a maximum tonnage per hour (textt/h) [4], [5].

• **Constraint Enforcement:** These values act as hard requirements, directly restricting the flow of material along the calculated **Production Arcs** [6]. If a calculated assignment suggests that the production goals **will not be met**, that assignment may be rejected [7], [8].

• **Recommended Buffer:** It is recommended to use a 10 **buffer** for all numerical targets to give the Assignment engine flexibility to meet the target and occasionally exceed it [1].

2. Priorities (Hierarchical Ranking)

Priorities are used to **create a hierarchy for the goals** that are met and dictate the **order of capacity allocation** [9].

• **Ranking System:** Shovels can be assigned priorities (High, Normal, or Low) [10], [11]. In an **undertrucked situation** (where insufficient trucks are available to cover all excavators), the optimization system uses this hierarchy to determine which shovels receive limited haulage resources first [11], [12], [13]. High priority shovels are the last to starve [14], [11], [15].

• **Ambiguity Leading to Inefficiency:** The core source states that the suggested use of priorities has, in some cases, **caused inefficient assignment decisions leading to excessive queuing** [1]. This strongly supports the idea that, because priorities are inherently less clear than fixed numerical goals, relying solely on them can confuse the assignment algorithm and lead to poor operational outcomes [1].

• **Priority Factors in Calculations:** In the Hexagon Joptimiser, shovel priority converts to a numerical factor (e.g., High = 1.25, Normal = 1.00, Low = 0.75) which, when multiplied by the desired coverage, influences the shovel's required haulage and perceived "need" for trucks [16], [17].

In summary, while priorities establish which resources are favored (e.g., excavators digging high-value material get preference), **Goals provide the clear, quantitative targets** that definitively define the objective function for the optimization algorithms, reducing the risk of unintended assignment consequences like excessive queuing that arise from the ambiguous nature of hierarchical prioritization alone [1].

--------------------------------------------------------------------------------

## Mine Production Plan: Optimization and Constraints

The **Production Plan** is the overarching strategy established by the optimization algorithms to maximize material movement and efficiency, while adhering to the specific needs and limitations of the mine site [1], [2]. In the larger context of **Production Planning & Constraints**, the Production Plan is the direct outcome of balancing the site's goals with the real-time operational constraints and physical limits of the mine [1], [3], [4].

The concept is central to both the DISPATCH/Modular Mining systems and the Joptimizer/Hexagon Mining systems, and dictates how truck assignments are generated automatically [1], [5].

1. Definition and Foundation of the Production Plan

The Production Plan is essentially a highly calculated, theoretical master plan that guides fleet operation throughout a shift [1], [6], [7].

• **Basis of the Plan:** The plan is **based upon the site’s overall goals** as set in the system through priorities and quantifiable objectives [1], [4]. If explicit numerical production requirements or goals are set, the assignment engine prioritizes solutions that meet them [8], [4]. If no explicit goals are set, the system defaults to maximizing tons moved while minimizing cost [8], [9].

• **LP Solution as the Plan:** In optimization models like DISPATCH or Joptimizer, the **Linear Programming (LP) layer** computes this Production Plan, often referred to as the **LP solution** [6], [7], [10], [11]. This solution is computed automatically by complex mathematical algorithms to establish the haulage flow paths and rates (tons per hour) that trucks should achieve [1], [12], [7], [13], [10].

• **Components of the Plan (Production Arcs):** The plan consists of optimized production circuits, each defined as a **Production Arc**, which is the **unique combination of the loading tool, processor (dump destination), and material** being hauled [14], [5].

2. Constraints and Inputs that Define the Plan

The resulting Production Plan is rigorously shaped by numerous constraints and configurable inputs, collectively categorized under production planning and constraints. These constraints prevent the purely mathematical optimization engine from disregarding practical, safety, and logistical requirements [9], [15], [16], [17].

A. Direct Production Targets (Goals and Coverage)

The most direct way managers influence the plan is by setting quantitative constraints:

• **Production Goals:** These are numerical production objectives (e.g., tons per hour, t/h) that restrict the material flow along the production arcs [14], [18]. If a truck assignment would cause production goals **not to be met**, the assignment may fail [19]. Goals specify "At least" a minimum and "No more than" a maximum material flow [20], [21].

• **Shovel Priorities:** Priorities (High, Normal, Low) are crucial in **undertrucked situations** (where there are insufficient trucks to cover all shovels) [1], [22]. High-priority shovels are the last to starve and are favored in the LP solution to receive haulage resources first [22], [23].

• **Desired Coverage Percentage:** This factor determines the maximum percentage (up to 150%) that LP attempts to cover a shovel's loading rate in the Production Plan [14], [24], [25]. Setting coverage above 100% intentionally **overtrucks** a shovel to minimize idle time [15], [26], [27].

B. Operational and Physical Constraints

The physical configuration and real-time operational status impose hard limits on the Production Plan:

• **Material and Grade Compatibility:** A central constraint is that material excavated by a shovel must be accepted by the intended dumping location [28], [29]. If materials are incompatible, the Production Arc for that path is not created [30], [31], [32].

• **Equipment Capacity:** The **Shovel Digging Rate** (continually measured in real-time) and the **Dump Capacity** (maximum tons/hr) constrain the total volume of material that can flow through a production arc [33], [12], [34], [35].

• **Continuity:** A fundamental constraint is that the total flow of material/trucks entering a node (shovel or dump) must equal the total flow exiting that node [36], [28], [37].

• **Road Network Health:** The presence of a **Best Path (BP)** between two points is necessary for a route to be feasible [38], [11]. This requires **accurate elevation data** at callpoints to correctly calculate gradients and thus travel times, ensuring the LP model is realistic [39], [40], [41].

C. Special Operational Constraints ("Other Considerations")

These considerations, while often dealing with specific scenarios, are integrated into the LP solution generation:

• **Blending Requirements:** LP enforces blending constraints to control the quality of material delivered to dumps/crushers [42], [43]. If blending requirements cannot be met, the assignment may be deemed incompatible [19], [44].

• **Scheduled Assignments (Delays/Maintenance):** Scheduled activities like **refueling** or **maintenance** (Delays) affect the availability of equipment. If equipment is unavailable, it is **removed from the Production Plan** [45], [12]. The system forecasts the duration of planned delays to ensure trucks aren't starved waiting for equipment to resume work [46], [47].

• **TKPH Constraints:** If a truck has a high TKPH warning, the LP output influences subsequent assignments by steering the truck toward shovels on haul routes with the **lowest average TKPH value** to limit tire wear, even though high TKPH does not typically cause assignment failure [19], [48], [49].

3. Monitoring and Execution

The Production Plan is dynamic and must be continuously checked and executed:

• **Automatic Recalculation:** The Production Plan is automatically recalculated at regular time intervals (default 600 seconds/10 minutes) and triggered by **event-based changes** such as alterations in shovel or dump status, grade changes, or road changes [50], [51], [52], [53].

• **Execution (DP Layer):** The **Dynamic Programming (DP) layer** uses the LP solution (the Production Plan) as its blueprint to execute assignments in real-time. DP constantly builds "future scenarios" to ensure balance and synchronization while striving to match the flow rates determined by LP [54], [55], [56], [57].

• **Monitoring:** Mine Controllers must monitor the resulting plan using tools like the **Trucking Indication (TI)** page, which graphically shows the production arcs and metrics like **Coverage** (actual trucks associated versus targeted need), to ensure the plan is being met effectively and adjust constraints if needed [58], [59].

--------------------------------------------------------------------------------

## Automated Mine Assignment Goals and Prioritization

The core function of the automated assignment system, whether it is **Cat MineStar Fleet/DISPATCH** or the Hexagon Mining **Joptimiser**, is to create a **Production Plan** that is centrally derived from, and continuously calculated to fulfill, the **site’s overall goals** for the shift [1-3]. The entire optimization process is fundamentally driven by adhering to these production goals and priorities, which translates directly into minimizing operational costs and maximizing efficiency [4-7].

The Production Plan as the Embodiment of Site Goals

The **Production Plan** is explicitly defined as being **based upon the site’s overall goals as set in the system through the system goals and priorities** [1]. If production requirements or goals are configured in the system, the assignment engine prioritizes its assignments to meet those numerical production objectives [5, 8-10]. If there are **no explicit production objectives configured in the system**, the optimization engine defaults to a strategy to **maximize tons while minimizing cost** given the current state of the mine model [5, 11].

The Production Plan is articulated as a constantly recalculated optimal solution—the LP Solution—that governs how trucks should be automatically allocated throughout the mine [1, 12-14].

Goals and Priorities as Direct Constraints

Site goals and priorities are implemented in the system as specific constraints that dictate the behavior of the assignment algorithms:

• **Production Goals:** These are numerical objectives, typically expressed in terms of tons per hour (t/h), that the dispatchers aim to achieve during the shift [9, 10, 15, 16]. The system attempts to meet minimum production targets and limits maximum production rates as set by these goals [10, 17]. When configured, assignment logic explicitly checks against these goals, and a compatible assignment may be rejected if it means the production goals **will not be met** [18, 19].

• **Priorities:** Priorities are set to establish a hierarchy for meeting goals and allow the system to allocate capacity effectively [1, 20]. For loading tools (shovels/excavators), priorities (High, Normal, Low) determine the **order of capacity allocation** [1, 21-23]. For example, high-priority shovels are the last to starve in undertrucked situations, ensuring they receive the necessary haulage resources first to satisfy their assigned production rate [23, 24]. Material priorities also apply, where a shovel digging a high-priority material will be favored over lower-priority materials [25, 26].

Configuration and Monitoring to Achieve Goals

Mine Controllers are tasked with constantly setting and monitoring these inputs to ensure operations align with the desired output:

• **Production Requirements Assistant:** This utility is used by Controllers to **view, edit, and create production requirements** (goals and priorities) that drive the overall assignment engine to achieve site goals [2, 20, 27].

• **Decision Support/Testing:** The system includes a **Decision Support** functionality, often described as a "sandbox," which allows users (Controllers or Supervisors) to evaluate the impact that proposed changes to production goals, priorities, and restrictions will have on production outcomes _before_ implementing them in the live system [28-30].

• **Troubleshooting Failures:** If an assignment fails, the **Assignment Context** tool provides explicit messages, including if an assignment was **"not chosen due to production requirements"** [18, 19], allowing the Controller to troubleshoot and verify if the intended operational goals are being correctly enforced by the algorithms.

By integrating site goals directly into the mathematical framework (Linear Programming layer), the system ensures that assignments are optimized not just for simple productivity (moving the most tonnage) but for achieving complex, predefined objectives, including blending requirements, maintenance scheduling, and efficient resource allocation [4, 6, 31].

--------------------------------------------------------------------------------

## Automated Optimization in Mining Production Planning

The sources consistently emphasize that key aspects of the **Production Plan** are **computed automatically by algorithms** within the fleet management systems (DISPATCH or Joptimizer), forming the intellectual core of the mine’s optimization strategy. This automated calculation aims to maximize efficiency and minimize costs, using real-time data and predetermined constraints to define the most productive operational flow.

The Role of Algorithms in Production Planning

The primary function of systems like DISPATCH or Joptimizer is to **maximize the production of the mine by computing truck assignments** based on the shovel capacity and utilizing complex mathematical algorithms [1-3].

The overall Production Plan is continuously calculated to **maximize the production outcomes** given the current state of the mine model [4-6]. This plan is a theoretical master plan (known as the **LP solution** in Modular's terminology or the Linear Programming layer) that defines optimized production circuits, or **Production Arcs**, and the corresponding **haulage flow paths and rates** (e.g., tons per hour) [4, 7-15].

1. Optimization Models

The calculation of the Production Plan relies on a multi-layered optimization model:

• **Linear Programming (LP) Model:** This layer creates the theoretical master plan (LP solution) by analyzing input variables and constraints to maximize overall truck productivity [3, 12, 13, 16-18]. The objective function of Joptimizer maximizes achievable production by minimizing the hauling requirements needed to cover digging rates in every feasible hauling route [13, 19].

• **Dynamic Programming (DP) Model / Dynamic Assignment Layer:** This layer executes the LP solution in real-time, assigning available trucks to shovels and dump locations with the goal of enforcing the determined LP Paths and achieving the calculated feed rates [3, 6, 20-27]. DP continuously builds **"future scenarios"** when generating assignments to ensure balance and synchronization while focusing on the least amount of lost tonnage [24, 28].

2. Automatic Calculation of Critical Inputs

Algorithms automatically calculate or update several critical inputs necessary for building the Production Plan:

• **Cycle Durations and Travel Times:** The assignment engine relies on continuously updated cycle component times to make assignment decisions [29, 30]. The **Best Path (BP)** model calculates the **shortest paths** between every two locations and feeds this data, including associated travel times, to the LP layer [7, 17, 31-33]. Travel times are calculated dynamically and automatically based on factors like gradient, average speed, and whether the truck is loaded or empty [30, 34-38].

• **LP Recalculation Triggers:** The LP solution is automatically recalculated at a **regular time interval** (e.g., every 600 seconds/10 minutes by default, configurable down to 60 seconds) to account for dynamic changes in the mine [14, 39-42]. Additionally, calculations are triggered by **event-based triggers** such as a shovel or dump status change, a grade change, or a road change [14, 27, 43, 44].

• **Dig Rates and Capacities:** The on-board system (JAMS or OEM) **continuously measures and updates** the shovel’s digging rate based on spotting time, load time, and tonnage produced [45-47]. The LP layer incorporates this actual dig rate as a constraint when calculating the shovel capacity and the required haulage for the Production Arc [45, 46, 48, 49].

• **TKPH:** The system **automatically calculates** the TKPH (Ton Kilometers Per Hour) value for trucks, monitoring it against thresholds for tire management [50-53]. This value is computed as a **weighted average** of empty and loaded paths and is updated at each LP calculation [54, 55].

3. Output and Interpretation

The automated computation produces outputs that guide operations:

• **Haulage Flow Paths and Rates:** The LP solution defines the required tonnage (flow rate) per hour for each operational circuit (Production Arc) [8, 9, 11].

• **Required Trucks at Coverage:** The LP Summary report automatically displays the calculated "LP Used Trucks" or "Required Trucks at Coverage," which represents the theoretical number of trucks of an average size needed to cover the operating shovels at their maximum utilization levels [56-60].

• **Assignment Steered:** Based on these calculated parameters, the DP layer dynamically generates automatic assignments in real-time, routing trucks to the lowest-cost option based on forecasted queue time, travel time, and priority factors [61-64].

Although the core plan is automatically computed, its accuracy relies heavily on **accurate user inputs** (e.g., elevations, priorities, desired coverage) and human management of exceptions and maintenance to ensure the mathematical model reflects reality [65-69]. If the system lacks accurate knowledge of the mine, it is unable to make the best decisions, resulting in issues like queues, idle loaders, and higher costs per ton [65, 70-73].

--------------------------------------------------------------------------------

## Mining Production Plan and Arc Allocation

The allocation of haul trucks within the mining system, managed by the Dynamic Assignment (DA) or Dynamic Programming (DP) layer, is governed by the larger strategic framework known as the **Production Plan**. This plan is inherently defined by the **combination of feasible production arcs** calculated in the Linear Programming (LP) layer, which establishes the required flow rates necessary to meet the site's overall goals and priorities [1], [2], [3].

The Production Plan as a Combination of Arcs

The overall Production Plan is essentially an automated, continuously calculated goal for maximizing production outcomes based on the current state of the mine model [1], [2], [4], [5].

1. **Definition of the Arc:** A fundamental element of this plan is the **Production Arc**, which is defined as the unique combination of the **loading tool, processor (dump destination), and material** being hauled [6], [2], [5], [7], [8], [9].

2. **LP Solution Foundation:** The Linear Programming (LP) model creates a theoretical master plan, known as the LP solution, which consists of optimized production circuits. These circuits are built from loaded and empty paths (or arcs) connecting shovels and dumping points [10], [11], [12], [13], [14]. The combination of these arcs determines the maximum production achievable by minimizing the cost of haul cycles while adhering to the constraints set by the mine [15], [16], [10], [17], [18], [19].

3. **Haulage Flow Rates:** The core output of the LP layer, derived from analyzing these arcs, is the definition of **haulage flow paths and rates** (tons per hour or similar metric) that trucks should achieve along each determined circuit [1], [20], [2], [11], [21], [22], [8], [17]. The LP calculates the required haulage capacity needed to cover the shovel digging rates across every feasible hauling route [23].

Factors Dictating the Combination of Arcs

The combination of arcs that forms the final Production Plan is highly sensitive to input and constraints applied by the Mine Controller or pre-configuration settings:

A. Capacity and Utilization Constraints

The structural and operational limits of equipment determine which arcs are feasible and how many trucks are dedicated to them:

• **Shovel Digging Rate and Capacity:** The shovel’s dig rate limits the material flow leaving the shovel [24], [25]. The system uses the concept of **Shovel Capacity** (or LP Dig Rate), calculated using the standard truck size, dig rate, and spotting time, to define the maximum output of a production arc [26], [27], [28].

• **Desired Coverage:** The **Desired Coverage Percentage** (0% to 150%) directly influences the capacity required for an arc [29], [30], [31], [32], [33]. If a high priority shovel is set to 150% coverage, the LP allocates resources to intentionally overtruck that arc (or circuit) [34], [35], [36], [26], [37].

• **Dump Capacity:** The capacity set on the processor (dump/crusher) acts as a cap on the flow rate of the corresponding loaded arc, ensuring continuity constraints are met [38], [39], [24], [40], [41].

B. Restrictions, Locks, and Compatibility (Feasibility)

Restrictions directly exclude specific combinations of arcs from the Production Plan:

• **Material Acceptance:** A crucial constraint is that the dump destination must **accept the material type** being dug by the shovel. If material is incompatible, the route is simply **not created** (i.e., the arc is deemed infeasible) [42], [43], [44], [45], [46], [47].

• **Locks:** Permanent locks (e.g., locking a shovel to a specific dump) **force the LP solution** to consider only specific dump locations, thereby restricting the possible arcs that can be generated [48], [49], [50], [51], [52].

• **Grade Restrictions:** Restrictions applied to a specific grade block prevent trucks loaded with that grade from being assigned to certain dumps [50], [51], [47], [53].

C. Priorities and Strategic Configuration

Priorities help the LP model decide which arcs receive limited resources first, especially in undertrucked situations:

• **Shovel Priority:** High-priority shovels are the last to starve in undertrucked situations, receiving haulage resource allocation first in the LP solution, thus favoring their associated arcs [54], [55], [56], [57], [58], [59], [60], [61], [62].

• **Proportional Feeding:** If enabled (typically in undertrucked scenarios), LP allocates feed rates proportionally across all selected shovels based on their maximum allowable production rates. This tactic ensures that all selected shovels receive some level of truck service, thus defining a combination of arcs that utilizes the entire fleet regardless of ranking [63], [64], [65], [66], [67], [68], [69].

Truck Allocation based on the Combination of Arcs

The **Dynamic Assignment (DA) Layer** takes the resulting Production Plan (the combination of weighted arcs) and uses it as a blueprint for making real-time truck assignments [21], [70], [71], [72].

1. **Enforcing Flow Rates:** DP enforces the paths and feed rates defined by the LP solution. When an empty truck requests an assignment, DP determines the **Neediest Shovel** and the **Neediest Route** (arc) associated with that shovel (the one with the largest deficit between required haulage and current haulage) [71], [73], [74], [75], [76], [77].

2. **Cost Minimization:** The choice of which truck to send to the neediest arc is based on the **least-cost calculation**, which heavily weights travel time versus expected idle time, ensuring the truck maximizes its productivity on the chosen arc [15], [78], [79], [80].

3. **Loaded Assignments:** For loaded trucks, DP assigns the dump based on a strict hierarchy (Truck Lock > Grade Lock > Shovel Lock). If no locks apply, the truck is assigned to the **neediest loaded LP Path (arc)** originating from the loading shovel, typically resulting in assignment to the closest dump that accepts the material [81], [82], [83], [84].

In summary, the **Production Plan** is the theoretical optimal arrangement of production arcs—combining loading tool, processor, and material—which dictates the flow rates. The combination of these arcs is influenced by nearly every operational constraint, priority, and parameter in the system, serving as the mandate for the real-time allocation of trucks via the Dynamic Assignment layer [1], [2], [5], [10], [85].

--------------------------------------------------------------------------------

## The Mining Production Arc and Optimization Engine

The concept of the **Production Arc** is fundamental to how the optimization engine (Linear Programming or LP layer) within a mine management system like DISPATCH or Joptimiser structures and executes the overall **Production Plan**. The Production Arc defines the complete, feasible link required for material flow, specifically combining a specific loading tool, a dump destination (processor), and the material being moved.

Definition and Components of the Production Arc

The Production Arc is defined as the **unique combination of the loading tool, processor (dump destination), and material** being hauled [1]. This concept is the elementary building block of the optimization strategy developed by the LP layer [2], [3], [4].

Each element of the Production Arc is critical to the calculation of optimal haulage flow rates:

• **Loading Tool (Shovel/Excavator):** The excavator digging the material at the source location.

• **Processor:** The destination where the material is dumped, typically a crusher, dump, or stockpile [5], [6].

• **Material:** The specific material type or grade being extracted and moved along the route [1], [6].

Role of the Production Arc in the Production Plan

The Production Plan itself is based on the site’s overall goals and priorities for a shift [2], [7]. The assignment algorithm automatically computes this plan as a combination of these production arcs, which are then used for the automatic allocation of trucks [2], [3], [8].

1. Optimization and Flow Rates

The LP model's primary function is to create a theoretical master plan (the LP solution) composed of optimized production circuits [9], [10]. These circuits consist of loaded paths (shovel-to-dump) and empty paths (dump-to-shovel) [11].

• **Haulage Requirements:** Each production arc specifies paths and flow rates (tons/hour) that trucks should achieve [2], [9], [3]. The LP layer calculates these required haulage rates necessary to cover the digging rates across every feasible hauling route [12].

• **Maximizing Production:** By minimizing the mathematical objective function, Joptimizer maximizes the production achievable, primarily by minimizing the required hauling tonnage within these arcs [13], [14], [15], [16].

• **Visual Representation:** In interfaces like the Trucking Indication page, users can see a graphical view of these production arcs, including the material flow rates (tons/hour) [17].

2. Constraints and Feasibility

The formation of a feasible Production Arc dictates whether trucks can even be assigned to a particular circuit. If an arc is not feasible due to constraints, assignments along that path fail [18], [19].

The LP layer checks several constraints during the creation of feasible routes that define the Production Plan [20]:

• **Material Constraints:** The dump destination (processor) must accept the material type being dug by the shovel [21], [22]. If the material is not accepted, the route is not created [22].

• **Continuity:** The total flow of material/trucks entering a node (like a shovel) must equal the total flow exiting that node [23], [24], [25].

• **Physical Constraints (Capacity):** The shovel’s dig rate acts as a constraint, limiting the total material leaving the shovel [26]. Dump destinations also impose constraints via maximum capacity (tons/hour) [27].

• **Locks and Restrictions:** Explicitly defined restrictions (e.g., locking a shovel to a dump destination, or setting grade restrictions) constrain the LP solution to only consider specific dumping locations as candidates for assignments, thus forcing specific Production Arcs [28], [29].

3. Shovel Coverage and Priorities

The operational status and goals of the equipment directly influence the Production Arc's calculated output:

• **Shovel Digging Rates:** The digging rate (measured in tons/hour) is continuously calculated and incorporated as a constraint, ensuring the total flow leaving a shovel does not exceed its maximum output [26], [30].

• **Priority and Coverage:** Configurable inputs such as **Shovel Priority** (High, Normal, Low) and **Percent Coverage** dictate the required tonnage flow for an arc [31], [32]. For example, adjusting coverage influences the calculated capacity of the arc, which dictates the haulage requirements and ultimately how many trucks Joptimizer attempts to assign to that arc [33], [34], [35]. A Production Arc that corresponds to a high-priority shovel will be favored when allocating limited haulage resources [33], [36].

In short, the Production Arc ties together the source, destination, and product being moved, acting as the fundamental theoretical path structure that the system uses to balance resources and assign trucks according to the mine’s current goals and constraints [1], [2].

--------------------------------------------------------------------------------

## Mining Truck Assignment Constraints and Optimization

The concept of **"Other Considerations"** encapsulates a crucial set of inputs, constraints, and special assignments that move beyond fundamental capacity balancing to ensure that automated assignment systems, such as DISPATCH or Joptimizer, meet the full complexity of daily **Production Planning & Constraints**. These considerations integrate safety, maintenance, scheduling, and material quality into the optimization model.

If these constraints are mismanaged or ignored, they can lead to **poor assignments**, causing trucks to queue, loaders to sit idle, and production recording to be negatively affected, ultimately resulting in **higher costs per ton** [1-5].

The sources detail four primary "Other Considerations" that heavily influence the assignment engine:

I. Scheduled Assignments (Maintenance, Breaks, and Fueling)

Scheduled assignments represent planned, mandatory interruptions to the normal production cycle that must be accommodated by the optimizer [6-8].

• **Refueling/Maintenance:** These are automatically generated assignments triggered when equipment surpasses **user-defined thresholds** [9-12].

    ◦ **Fuel Logic:** The system uses two critical thresholds: **Fuel Low Percent** (where Joptimizer starts _considering_ steering the truck toward a fuel station if the queue is low) and **Fuel Empty Percent** (where the truck is immediately assigned to the nearest fuel station, ignoring the queue capacity) [10-16].

    ◦ **Assignment Override:** Fuel and maintenance assignments generated automatically by the system **override** standard DP assignments [17-19].

• **Breaks/Tiedowns:** Scheduled time-based assignments (like shift changes or lunch breaks) force the assignment engine to balance immediate production with upcoming downtime [6-8]. The Dispatcher sets **Required At, Arrive After, and Arrive Before** times to ensure adherence [17, 20-23].

• **Impact on Assignment:** If a truck cannot arrive at a scheduled assignment destination outside the defined time window, or if there is no capacity at the destination (like a fuel bay), the assignment will be rejected, and the **Assignment Context** will reflect this reason [17, 24]. Conversely, if a needed machine (like a loading tool) is on a scheduled delay, the system evaluates the expected delay duration and may still assign trucks if they are forecast to arrive only shortly after the delay ends [25, 26].

II. Blending Requirements

Blending constraints are essential for controlling the material quality delivered to dumps or crushers, often overriding simple tonnage maximization goals [27, 28].

• **Enforcement:** Joptimizer enforces blending constraints using the Linear Programming (LP) layer [27, 29]. This ensures that the material flow maintains a desired composition within set minimum and maximum quality values over a specified **control mass or control interval** [27, 30-33].

• **Grade and Material:** Blending relies on detailed **grade** (e.g., %Au, %Cu) and **material type** information [31, 34, 35]. If a truck is loaded with material incompatible with the dump's blending criteria, the assignment may fail, or the shovel may be starved to prevent non-compliance [36-40].

• **Proportional Sharing (LP):** The system can use the **Proportionally Share Materials Dumping Capacities among Shovels** option to lower feed rates of shovels feeding constrained crushers, ensuring the limited dumping resources are shared and preventing shovels from becoming unused due to lack of capacity [29, 41-43].

III. TKPH Constraints (Tire Management)

TKPH (Ton Kilometers Per Hour) is a safety and maintenance constraint that the system manages to protect expensive tires from overheating [44-48].

• **TKPH Monitoring:** The system continuously monitors the calculated average TKPH against a defined **TKPH Warning Threshold** [49, 50].

• **Assignment Logic:** If a truck is flagged for a TKPH warning, the assignment algorithm automatically restricts assignments to shovels located on routes with the **lowest average TKPH value** [17, 49-55]. This often steers the truck toward an **alternate, shorter haul or a slower speed route** to limit tire wear [50, 51, 56-58].

• **No Assignment Failure:** Importantly, a high TKPH value will generally **not cause an assignment failure** outright; instead, the system influences the path selection toward safer options [46, 51, 59].

IV. Production Planning Inputs

The overall **Production Plan**, including **Goals and Priorities**, directly influences how the optimization engine prioritizes assignments [7, 60].

• **Production Goals and Rates:** If explicit production requirements are set, the assignment engine prioritizes solutions that meet these goals (e.g., maximizing tons moved or meeting specific throughputs at a crusher) [60-63]. If a truck fails to achieve a scheduled assignment due to blending or capacity limits, the assignment context reflects that production goals will not be met [17, 64].

• **Shovel Priority:** By configuring shovels with **High, Normal, or Low priorities**, the dispatcher influences which shovels are prioritized to receive limited trucks in under-trucked scenarios [65-68]. High-priority shovels are the last to starve [69-75].

• **Coverage Factor:** The **Desired Coverage Percentage** (0% to 150%) dictates the maximum percentage of a shovel's loading rate that LP should cover in its solution [62, 76-80]. Setting a shovel's coverage to 150% intentionally **overtrucks** it to minimize idle time (hang time) for high-priority equipment [81-85].

These considerations demonstrate that optimal truck assignment requires far more than just finding the nearest truck for the nearest shovel; it demands complex, multi-layered constraint management to align real-time dynamics with long-term mine planning objectives.

--------------------------------------------------------------------------------

## Scheduled Assignments in Dynamic Mining Optimization

The sources define **Scheduled Assignments** as vital "Other Considerations" within the dynamic mining optimization strategy, especially for maintenance activities like **refueling**. These assignments are planned interruptions to the normal production cycle designed to ensure equipment availability and cost efficiency by preventing critical failures or delays.

In the larger context of how assignment decisions are made, scheduled assignments function as planned constraints that the automated assignment engine (like DISPATCH or Joptimizer) must incorporate alongside production goals, blending requirements, and technical constraints like TKPH.

I. Types and Purpose of Scheduled Assignments

Scheduled assignments are instructions that direct a haul truck to a destination (Loading Tool, Processor, or **Station**) to perform a specific activity [1]. They fall into two main categories:

1. **Scheduled Automatic Assignments:** These are enacted when a **pre-configured time window can be met**, and the truck's load state matches given criteria [2]. These assignments are typically time-based and are used for routine events like **fueling** and **preventive maintenance** [3]. The system determines the optimal time to send the truck to the destination to meet the target time [3], allowing the mine to **optimize production outcomes by continuing to truck materials until the very last moment** [4].

2. **Scheduled Manual Assignments:** These assignments are triggered by a **specific load state or activity change** of a truck, such as acquiring fuel or picking up a load at a designated spot [5], [6]. This is essentially a manual directive for a future action and is triggered when the activity changes to the specified state [2].

Scheduled assignments are monitored in the **Scheduled Assignment Monitor** [7]. Active assignments are marked as Green, while those impossible to achieve will be marked as Late (Red) [7].

II. Automated Fueling Assignments (A Key Scheduled Assignment)

The primary scheduled activity detailed is **Automatic Refueling Assignments**, which is designed to prevent trucks from running out of fuel (which can damage components like injectors) and to minimize unnecessary downtime spent fueling [8], [9], [10].

A. Optimization and Cost Savings

Automatic refueling assignments aim to optimize the fueling process by **strategically setting refueling level goals** [10].

• **Production Gains:** Significant production gains can be realized by reducing the frequency of fueling activities and increasing the time between delays [10]. For example, lowering the minimum refueling capacity from 30% to 10% could drastically reduce total annual fleet downtime dedicated to fueling [10].

• **Trigger Mechanisms:** Automatic assignments are triggered when the truck's fuel level drops below **predefined thresholds**, usually configured on a truck class basis [11], [12], [13].

    ◦ **Fuel Low Percent:** When the fuel falls below this initial trigger (e.g., 25%), Joptimizer starts **considering** assigning the truck to the nearest available fuel bay, provided the queue capacity has not been exceeded [14], [9], [12], [15]. The system may prioritize dump/shovel assignments that are near or on the way to a fuel station [12].

    ◦ **Fuel Empty Percent (Critical Fuel Level):** If the fuel level drops below a second, lower threshold (e.g., 10% or 15%), the truck is immediately assigned to the nearest fuel station **regardless of queue capacity** at that location [16], [17], [13], [15].

• **Fuel Assignment Process:** A truck assigned for refueling is placed in a **DELAY** status with a specific reason code upon arrival at the fuel bay [18]. Once refueling is complete, the truck's fuel tank level is reset to capacity, and a fuel record is created [18], [19].

B. Timing and Scheduling Parameters

Scheduled assignments utilize precise time parameters to control when the assignment occurs:

• **Required At:** This time is **always included** and represents the target arrival time at the destination [3], [20]. The Assignment engine attempts to get the truck to the destination as close as possible to this time [21].

• **Arrive After / Arrive Before:** These optional fields allow the dispatcher to provide a specific time window for arrival [3], [20]. If the "Arrive After" time is omitted, the assignment might trigger too early [22].

• **Accuracy Levels:** The system's responsiveness to meet the _Required At_ time depends on the configured accuracy setting (Strict: within 5 minutes; Normal: within 10 minutes; Flexible: within 20 minutes) [20].

III. Impact of Scheduled Assignments on Optimization

Scheduled assignments act as **high-priority constraints** that influence the Dynamic Programming (DP) model's decision-making process:

• **Override DP:** Automatic fuel and maintenance assignments **override** standard DP assignments when the criteria are met [23].

• **Context for Assignment Failures:** When troubleshooting why a truck did not receive an expected assignment, the **Assignment Context** will explicitly list if the failure was due to the truck needing to wait for a delay to end at a destination, such as a **fuel bay**, related to a scheduled assignment [24].

• **Delay Configuration:** For scheduling specific actions (like mandatory breaks or refueling) upon arrival, the assignment configuration includes options for setting an **On Arrival Delay Type** and enabling the **Assignment Delay** flag [25]. This signals to the system to hold the truck at that location for the predetermined duration, impacting future assignment calculations for other equipment.

• **Time Constraints:** If the scheduled assignment time window (Arrive After/Arrive Before) is set too narrowly, it can lead to **assignment failures** because the system determines it cannot meet the constraint [26], [27]. Conversely, setting the appropriate constraints allows Joptimizer to efficiently manage the haul cycle around necessary interruptions [26].

In essence, scheduled assignments ensure that non-production tasks essential for equipment health and regulatory compliance are integrated into the optimized mining plan, minimizing the overall negative impact on productivity [28].

--------------------------------------------------------------------------------

## Mining Truck Assignment Scheduling and Refueling Optimization

The sources identify **Scheduled Assignments** (including those for refueling) as a critical category of constraints that the mining optimization system (specifically the Dynamic Programming (DP) or Joptimizer layer) must actively manage, alongside other considerations such as blending requirements and TKPH constraints, to ensure efficient operation and adherence to maintenance schedules [1-4].

In the larger context of system inputs and assignment logic, scheduled assignments represent predictable, planned operational interruptions that temporarily influence the automatic truck assignment process to address specific needs, often involving a time component [1, 4, 5].

I. Types and Configuration of Scheduled Assignments

Scheduled assignments are classified differently based on how they are initiated:

1. **Scheduled Automatic Assignment (Time-Based):**

• These assignments are designed for future events and are typically time-based [6, 7]. The Controller sets a target arrival time using **Arrive After, Required At, and Arrive Before** windows [8, 9].

• The system calculates the optimal time to send the truck to the desired destination to meet the target time as closely as possible [8, 10].

• This type of assignment is commonly used for predictable activities such as **fueling** (refueling) and **preventive maintenance** [4, 5].

• The system’s effectiveness is tied to the **Scheduled Assignment Arrival Time** setting (Strict, Normal, or Flexible), which defines the level of accuracy required (e.g., within 5, 10, or 20 minutes of the required time) [11].

2. **Scheduled Manual Assignment (Activity-Based):**

• These assignments are triggered by a specific load state or activity of a truck, typically resulting in the truck receiving a manual directive for the next action, such as obtaining fuel or picking up a load at a specific location [2, 12].

• They are enacted when the truck's activity transitions to a specified activity or load state [2, 6].

II. Refueling as a Primary Example

Refueling assignments are a major example of scheduled assignments designed to prevent costly downtime and injector damage caused by critically low fuel levels [13, 14].

• **Objective:** The assignment algorithm is designed to **optimize the fueling process** by extending the time between fueling delays, resulting in production gains and fuel cost savings [14, 15].

• **Triggers and Thresholds:** Trucks are configured to receive automatic refueling assignments based on predetermined thresholds, typically configured on a truck class basis [16].

    ◦ **Fuel Low Percent:** When the fuel level drops below this first trigger, the system begins considering assigning the truck to the nearest available fuel bay, provided the queue capacity is not exceeded [17-19]. Assignments generated at this point may favor routes to fuel bays that are "fuel-convenient" [18].

    ◦ **Fuel Empty Percent (Critical Fuel Level):** If the fuel level drops below this second, lower threshold (e.g., 10% or 15%), the truck is immediately assigned to the nearest fuel station **regardless of queue capacity** at the station [17, 19-23].

• **Assignment Process:** Trucks assigned for refueling are typically placed into a **DELAY** status with an appropriate reason code (e.g., "Refuel") upon arrival at the destination [17, 24, 25]. Once fueling is complete, the truck status changes, the fuel tank level is reset to capacity, and a fuel record is created [17].

• **Parameters:** Successful configuration involves determining **fuel capacities**, **refueling thresholds**, and **fuel bay capacity** [16, 23, 26]. The **Refuel Window** is a time value that helps strategically set the scheduled assignment, allowing the system to wait a period of time after the truck hits the low level before sending the assignment [27].

III. Role in Optimization and Constraints

Scheduled assignments form a distinct group of constraints that directly affect how the DP model allocates resources [1, 4].

• **Assignment Override:** Fuel and maintenance assignments generated automatically by the system **override** standard DP assignments, ensuring mandatory service tasks are prioritized [4, 28].

• **Assignment Context:** When troubleshooting, the **Assignment Context** tool reveals why a potential assignment (that was not chosen) was rejected, often listing the reason that the truck needed to wait for the destination (e.g., loading tool, processor, or **fuel bay**) to end a delay associated with a scheduled assignment [29].

• **Timing Conflicts:** The complexity of scheduled assignments, particularly at shift changes (which are treated similarly to breaks and tiedowns), requires careful attention to the timing window (Arrive Before, Arrive After) to ensure they do not become too narrow, which can cause assignment failures [30, 31].

In essence, while the main goal of the assignment engine is productivity, **scheduled assignments allow the system to incorporate necessary non-productive tasks** (like maintenance and fueling) into the optimized plan, preventing greater productivity losses later [4, 5].

--------------------------------------------------------------------------------

## Mining Optimization Blending Requirements and Constraints

The sources identify **Blending requirements** as a major operational constraint and input that the mining optimization system (such as DISPATCH or Joptimizer) must consider when generating assignments, particularly for haul trucks moving material from shovels to processors or dumps. In the context of **Other Considerations** that influence the Dynamic Programming (DP) or optimization engine, blending ensures that the material delivered meets specific quality or quantity targets.

Role of Blending Requirements in Optimization

The need to satisfy blending requirements is a powerful constraint enforced by the Linear Programming (LP) layer of the optimization system, which subsequently dictates the real-time assignments made by the Dynamic Assignment (DA) or DP layer [1-4].

1. **Enforcement:** Blending constraints are designed to **control the quality of material** produced at specific dumps or crushers [1, 3-5].

2. **Assignment Failure:** If a compatible dumping location (dump, crusher, or stockpile) is not chosen for a loaded truck due to blending, the **Assignment Context** message will explicitly state that the assignment was "not chosen due to blending requirements" [6, 7].

3. **Impact on Shovel Usage:** If a shovel is digging an ore grade that exceeds the capacity of the blending constraints (such as a crusher's capacity), the system may **"unuse" or starve** that shovel to prevent blend non-compliance [3, 4, 8-10]. Conversely, if a shovel is digging material critical to the blend, it may be prioritized.

Configuration and Components of Blending

To incorporate blending into the optimization logic, the mine model must be configured with detailed material information and specific blending models, typically managed via the **BlendMod Utility** [11-14]. Configuration tasks are often the responsibility of the **Builder** or a Mine Planning Engineer [15-18].

Key components and definitions include:

• **Grades and Material Qualities:** Blending relies on detailed material qualities, referred to as **grades** (e.g., acidity, sulfur content, %Au, %Cu), which are first specified for each material block, usually obtained from the mine planning system [3-5, 19-22].

• **Blending Model (Recipe):** A Blend Model, or "blending recipe," specifies the target material composition and limits for the material flow to a dump or crusher [3, 17, 22, 23]. The blending model comprises various parameters [22, 24, 25]:

    ◦ **Blending Control Mass/Interval:** Defines the tonnage (mass) or time (interval) over which the blend must remain within specifications [22, 26]. Setting the control mass to a larger amount allows for greater deviations from the blend specifications for individual loads [27-29].

    ◦ **Quality Values:** Sets the minimum and maximum acceptable quality percentage for each component [22, 30].

• **Types of Blending:** The sources specify three primary types of blending [27, 31-33]:

    ◦ **Discrete Blending (Material Blending):** Focuses on managing the ratios between different **discrete grades** or material types (e.g., High Grade, Low Grade) to ensure the desired proportions are delivered over a defined quantity (mass or time) [27, 32, 33].

    ◦ **Continuous Blending:** Ensures that **continuous qualities** (e.g., gold content) remain within set maximum and minimum limits over a defined control quantity [27, 32, 34].

    ◦ **Batch Blending (or Category Blending):** This method ensures that the blend meets specifications only upon completion of the entire production target (e.g., 100,000 tons total), offering flexibility in excavator use but posing a higher risk if equipment breaks down [3, 32, 35, 36].

Impact on Assignment Logic

The configuration and status of blending dictate specific actions within the assignment process:

• **Load Path Feasibility:** If a shovel digs a material that is **not accepted** at a specific dump location, the LP solution simply recognizes that route as non-feasible, ensuring trucks are not routed there [37-40].

• **Hard Constraint Enforcement:** When blending is active, LP creates constraints that force material delivery to adhere to the specified quality parameters [1, 5, 22]. This means Joptimizer is actively directing material flow to meet minimum production targets and limit maximum production rates of certain grades [10, 41].

• **Assignment Steered:** Since the DP model attempts to execute the LP solution, trucks are intentionally directed to or away from dumps to maintain the required material ratios, even if this means dumping out of spec temporarily, provided the blend is fixed by the end of the control period [1, 42].

• **Troubleshooting:** If the blending requirements are causing a shovel to be underfed, it indicates that production constraints are the root cause, and typically **no immediate manual intervention** is required (unless capacity constraints must be adjusted) [43, 44].

In essence, blending requirements operate as powerful **cost-minimization constraints** because they force the highly efficient optimization engine to meet complex material quality targets, overriding the simple goal of maximizing tonnage moved, potentially leading to assignment failures if the configuration is flawed [1-3, 45].

--------------------------------------------------------------------------------

## Mining Operations: TKPH Tire Management and Assignment Logic

The sources highlight that **TKPH (Ton Kilometers Per Hour)**, or its imperial equivalent TMPH (Ton Miles Per Hour), is a critical safety and operational parameter related to tire performance, and managing it forms one of the essential constraints considered by the automated assignment system, alongside other optimization variables.

Definition and Purpose of TKPH

TKPH is defined as a **measurement of a tire’s capacity to handle its operational workload**, specifically factoring in weight and speed to prevent overheating and premature failure [1]. Tire manufacturers assign an upper limit TKPH rating to each tire based on its size, construction, tread type, and rubber compound [1, 2]. Since **tires are one of the top expenses for mine sites** (costing between 20,000–60,000 per tire), effective tire management is vital for job sustainability [3, 4].

The formula for calculating TKPH generally involves: Gross Truck Load (Ton) x Distance Travelled (km) / Time Taken (hr) [5]. The calculation may be based on an average obtained over a four-hour period [5]. The calculated average is distributed among the individual tires based on calculated weight distributions [5].

TKPH as an "Other Consideration" in Assignment Logic

In the context of mining operations and assignment logic, TKPH is explicitly listed as one of the **Other Considerations** that the assignment engine monitors when determining the most efficient assignment for a haul truck, alongside scheduled assignments and blending requirements [6].

Managing TKPH involves setting parameters and thresholds:

1. **TKPH Thresholds and Configuration:** Mine Builders are responsible for entering **TKPH Thresholds** based on manufacturer specifications, which is done during machine setup or when tires are changed [7, 8].

• The **TKPH Lowest Rated Tire** value must be entered to prevent the truck from exceeding performance specifications [8].

• The **TKPH Maximum Percentage** defines the maximum allowed percentage of TKPH before an alarm is raised, typically set at 90%, or 80% for larger trucks [9].

• The system uses the **Max TMPH/TKPH** value for the tire vendor warranty to generate an exception if the current value is exceeded [10-12].

• TKPH calculations can incorporate ambient temperature readings, which must be entered into the system [13, 14].

2. **Assignment Logic Under TKPH Warning:** When a truck's calculated average TKPH exceeds the **TKPH Warning Threshold**, the assignment logic initiates a specific response:

• **Assignment Steered:** The truck is **assigned to shovels on hauls that have the lowest average TKPH value** for the routes to that shovel [15, 16]. This restriction mechanism aims to route the truck on an **alternate, shorter haul or a slower speed route to limit tire wear** [7, 17, 18].

• **TKPH Assignment Percentage:** The system uses a configurable **TKPH assign percentage** (set in the fuel consumption table) to restrict the truck under warning to routes with average TKPH values lower than the specified percentile (e.g., if set to 25%, the truck is restricted to the lowest 25% of haulage cycles) [15, 16, 19]. The final assignment decision from this reduced list of routes is based on distance and projected idle cost [15, 20].

• **No Hard Lock:** A truck registering high TKPH **will not cause failed assignments**; the assignment model merely steers the truck toward safer routes to mitigate tire wear [3, 7].

3. **TKPH and Cost Minimization:** While TKPH management is primarily a maintenance and safety concern, the goal is to **improve productivity by maximizing material movements while minimizing the total amount of hauling by the trucks** that leads to high tire stress [18].

The Controller's role includes continuously **monitoring TKPH** values (often visible in the **TKPH Monitor column** in utilities like the Fleet Update Assistant) and intervening according to site procedures if thresholds are reached, such as by placing the truck on a short haul or slowing it down, while deliberately avoiding stopping the truck, as this can temporarily increase tire temperature [3, 14, 17, 21, 22].

--------------------------------------------------------------------------------

## Dynamic Travel Time in Fleet Optimization

The sources extensively discuss the calculation and use of **Travel Times** for mobile equipment, particularly haul trucks, emphasizing that accurate travel time is a critical component influencing the optimization of operations and overall assignment decisions. Travel time calculations are continuously updated and are integrated throughout the entire fleet management system (like DISPATCH or Joptimizer).

In the larger context of **Other Considerations** that drive the assignment algorithms, travel times form a core input alongside factors like equipment constraints, production goals, and machine status.

1. Calculation of Travel Times

Travel times are calculated dynamically and continuously for all segments of the haul cycle, particularly for empty and loaded trucks.

• **Best Path (BP) Model:** The calculation of the best path (BP) between two points primarily relies on determining the **shortest distance** as a criterion, using the scale representation of the mine pit found in **MineGraphics** [1-4]. BP calculates the shortest paths between every two locations in the pit and supplies this data, along with associated travel times, to the Linear Programming (LP) model [5, 6].

• **Design vs. Dynamic Travel Times:** When a road segment is initially created, the system calculates a **design travel time** based on its distance, direction, and gradient [7]. As trucks traverse each road segment, the system updates the statistical information, yielding **dynamic travel times** [7]. The assignment engine uses dynamic travel times for the shortest path calculations; only if dynamic data is unavailable are the design times used [8].

• **Factors Influencing Calculation:** Travel time is the time in seconds that trucks spend traveling on a given route, factoring in the **gradient**, **average speed**, and whether the truck is **traveling empty or loaded** [3, 9].

    ◦ **Average Speeds:** The system maintains an **Average Speeds table** that is constantly updated as trucks travel the road network, storing the average speed for each **gradient** and **road category** (e.g., Surface, Inpit) for each truck type, whether loaded or empty [9-11].

    ◦ **Road Categories and Gradients:** The dispatchers must ensure that new road segments are assigned the **correct road category** [11, 12]. Furthermore, road gradients are determined by the **elevations of their end points (callpoints)** in the database, meaning incorrect callpoint elevations can corrupt the average speed calculation and lead to "wrong" assignments [3, 13-15].

2. Integration into Optimization and Assignments

Travel times are essential inputs for the Joptimizer/DISPATCH assignment engine, directly impacting cost minimization and assignment decisions.

• **Cost Minimization:** The assignment engine aims to maximize production by minimizing the cost of each haul cycle component [16, 17]. For an empty truck, the cost of assignment is calculated by adding the forecasted **travel time** and **idle time** (queue time) costs, along with a **shovel priority factor** [18-20]. The truck with the lowest calculated cost is chosen for assignment [21]. By default, the travel time cost factor (1.5) is higher than the idle time cost factor (1.0), meaning Joptimizer favors routes with **shorter travel times** [20, 22].

• **Assignment Decision Inputs:** Accurate travel times are explicitly listed as an "Other Consideration" that the assignment engine looks at when determining the best assignment for machines, alongside scheduled assignments, blending requirements, and **TKPH constraints** [23].

• **Expected Time of Arrival (ETA):** The system continuously calculates the ETA at the next assigned destination by using the Best Path and the calculated average road segment speeds [24]. The ETA is vital for **forecasting shovel hang and truck queue times**, which directly drive the assignment algorithm [24, 25].

• **Haulage Requirements:** In the Linear Programming (LP) layer, truck travel time is factored into determining the required haulage for a specific circuit, a critical calculation used to achieve target production rates [26, 27].

3. Travel Time as a Troubleshooting Metric

The accuracy of travel times is so critical that discrepancies often point directly to issues in the underlying mine model, hardware, or operator behavior.

• **Monitoring Late Equipment:** If trucks are late in completing their assigned travel segments, it can indicate a problem with the road network or average speeds, leading to late alerts [25].

• **Misroute Detection:** If a truck travels off the assigned path (the "green line" on the operator's screen), the system logs a **misroute exception** and requests a new reassignment, showing that the system is constantly enforcing adherence to the optimal calculated path [28-30].

• **Impact of Operator Behavior:** If an operator stops on a haul road without entering a delay, it **negatively impacts the dynamic travel time** for that road segment, potentially compromising future assignment decisions [12, 31]. Inaccurate travel times can also result from **truck operators delaying in pressing field computer buttons** during the haul cycle [32].

• **Road Network Integrity:** Issues like incorrect callpoint elevations, closed roads, incorrect road categories, or redundant restrictions can lead to invalid Best Path calculations, thus resulting in inaccurate travel times and poor assignments [15, 33, 34]. Mine Controllers must routinely monitor the road network using tools like **GPS Traces** to confirm that road alignment matches the actual paths traveled by trucks [35-38].

In summary, **travel time** is a core operational metric that anchors the functionality of the automated assignment system, directly impacting costs by ensuring trucks spend the least amount of time traveling empty and maximizing their productive use [16]. Its accuracy must be diligently maintained by Mine Controllers to avoid optimization failures [39].

--------------------------------------------------------------------------------

## MineStar Data Integrity for Optimization and Operations

The operational effectiveness of the **Cat MineStar System Fleet** and **DISPATCH** optimization relies fundamentally on maintaining high **Data Integrity** across all mine information components. Poor data quality in the system directly undermines the efficiency of **Operations & Assignments**, leading to measurable losses in productivity and increased costs.

The Central Importance of Data Integrity

The function of the Fleet or DISPATCH system is to optimize mine operation based on real-time data to **maximize efficiency and reduce operating costs** [1, 2]. The system achieves this by making decisions based on its "knowledge of the mine," much of which comes automatically from the office software, but several critical functions require user interaction [3-7].

If the system lacks **accurate knowledge of the mine**, it is unable to make the best decisions [3-5]. This deficiency results in tangible operational penalties:

• Trucks may be sent **poor assignments** [3-5].

• It can result in **trucks queuing**, **loaders sitting idle**, and **misdirected loads** [3-6, 8].

• Production recording is also affected [3-5].

• Ultimately, this leads to **higher costs per ton** [3-5, 8].

The continuous responsibility for upholding data integrity primarily falls on the **Mine Controller** and the **Builder/Champion** roles [3, 9-14].

Key Mine Information Components Requiring Integrity

Data integrity must be maintained across all aspects of the virtual mine model, encompassing hardware, system configuration, and real-time operational data.

1. Equipment Status and Availability

A machine's **Status** (Ready, Down, Delay, Standby, Shiftchange) is critical because it determines the equipment's eligibility for automatic assignment [15-21].

• **Accurate Status Reporting:** Inaccurate status information leads to poor planning and poor performance [22]. For example, if an operator does not correctly end a delay status, the system may still think the machine is on delay, negatively affecting truck assignments [23].

• **Delay Management:** Delays must be properly entered and ended. The assignment engine (Dynamic Programming, DP) considers expected delay times when issuing assignments, potentially sending trucks to a delayed loading tool if it expects the delay to end before the trucks arrive [24, 25]. Controllers must ensure delay information accurately reflects the duration of the interruption [26].

2. Spatial Mine Model and Locations

The effectiveness of the Best Path (BP) algorithm and Estimated Time of Arrival (ETA) predictions depend on the accuracy of the mine's spatial data [27-30].

• **Road Network Health:** The foundation of the optimization system is the **"healthy road network"** [28]. This involves having accurate road segments, elevations for callpoints (CPs), blasts, and dumps [30-32].

    ◦ If **callpoint elevations are incorrect**, Joptimizer may make "wrong" assignments because the calculation of travel time (a critical cost factor) relies on gradients derived from elevation data [29, 30, 33, 34].

• **Location Management:** When equipment moves, the Controller must manually update its location (e.g., changing a shovel's destination via the Loading Tool Assistant) to prevent road segments from attaching to the incorrect destination, which negatively affects truck assignments [35]. Polygon boundaries for locations like dumps and stockpiles also ensure **precise destination control** and accurate activity changes when trucks enter the boundaries [36-38].

3. Production Data (Cycles, Rates, Payloads)

The data derived from hauling cycles forms the basis for KPI calculation and long-term reporting.

• **Cycle Integrity:** Truck cycles contain **source information used for reporting**, making it **vital that the information is correct by the end of the shift** [39-43]. Inaccurate or incomplete cycles lead to incorrect production recording [3].

• **Activity Timing:** Accurate measurement of activities like spot time, load time, and dump time is crucial, as these times are inputs for calculating overall cycle duration, shovel digging rates, and LP solutions [44-46]. Errors, such as **truck operators delaying in pressing field computer buttons** or mechanical issues like a malfunctioning truck bed switch, generate cycle errors (e.g., missing information, time duration errors, activity overlaps) that Controllers must correct using the Cycle Editor [41, 47-50].

• **Payload and Material:** Every cycle must contain **material and payload information** [51]. Incorrect material type information leads to misdirected assignments (e.g., waste sent to an ore crusher) [52, 53]. The assignment engine needs to know the **material grade** of the load to make correct dump assignments [54, 55].

Data Integrity Management and Troubleshooting

Mine Controllers routinely perform checks and corrective actions to maintain data integrity:

• **Routine Checks (Start of Shift Checklist):** Controllers must check statuses, locations, material types, and existing locks/bars before the shift begins to ensure operational compatibility with the production plan [56-59].

• **Troubleshooting Tools:** The system provides tools like **Assignment Context**, which explains why an assignment failed (e.g., due to delays, restrictions, incompatibilities), allowing controllers to identify the root cause [60-62]. The **LP Troubleshooting Analyzer** helps diagnose problems related to shovel, dump, and path configurations affecting the LP solution [63-65].

• **Corrective Actions:** Controllers actively engage in data correction by manually:

    ◦ Updating status and location of equipment in utilities (like **TruckMod Utility** or **Loading Tool Assistant**) [66, 67].

    ◦ Editing historical cycle and delay records in the **Shiftedit Utility** or **Delay Assistant** to correct operator input errors or system faults [68-70].

• **Synchronization:** Historical delays and cycle delay activities are stored separately but **must be synchronized**; this process is often automatic but may require manual checks and re-synchronization by the Controller [69, 71, 72].

In essence, data integrity is not a static state but a dynamic process of continuous collection, automated validation, and active management by the Mine Controller to ensure the highly complex optimization algorithms of the Cat MineStar/DISPATCH system yield productive and cost-effective assignments [13, 73-75].

--------------------------------------------------------------------------------

## Haul Truck Management and Data Integrity Utility

The **TruckMod Utility** (or Truck Utility/Truck Assistant) serves as the dedicated administrative interface for **Haul Trucks**, making it an indispensable tool for maintaining **Data Integrity** within the mine fleet management system. The accuracy of the information managed via this utility is directly linked to the performance of the optimization algorithms and the reliability of production reporting.

1. The Critical Role of Accurate Truck Data

Data integrity is foundational to the efficiency of the entire **Fleet** system [1-9]. If the system lacks accurate knowledge of the mine (including equipment configuration and status), it leads to **poor assignments**, resulting in trucks queuing, loaders sitting idle, misdirected loads, and ultimately **higher costs per ton** [1-4, 9, 10]. The Controller is directly responsible for ensuring that cycle data, status, and configuration information entered through the TruckMod Utility are correct [11-26].

2. Key Data Components Managed via TruckMod Utility

The TruckMod Utility allows controllers and maintenance personnel to view and modify static and dynamic data influencing assignment logic:

A. Operational Status and Assignment Availability

The utility displays and allows the manual modification of a truck's current **Status** (Ready, Down, Delay, Standby, Shiftchange) and **Reason** for the status change [27-38].

• **Impact on Assignment:** The status dictates whether a truck is eligible for assignment calculations. Trucks must be in the **Ready, Shiftchange, or Delay** status to be considered eligible for assignments, while **Down or Standby** trucks are excluded from optimization planning [33, 39-43].

• **Manual Intervention:** If an operator fails to enter Standby when logging off a truck that is not scheduled for the upcoming shift, the dispatcher must use the TruckMod Utility or similar tools to manually set the status to **Standby** to prevent the system from considering the truck in its calculations [29, 31, 44-48].

B. Physical Location and Assignment Context

The utility tracks the truck's operational context, including its location and current assignment status [37].

• **Location/Destination:** The utility displays the **Location** and **Next Loc (Location)**/Destination fields [27, 30, 33, 38, 49-53]. This location data must be manually updated by the Controller if, for example, a downed truck is moved to a workshop, ensuring accurate operational context [49, 50, 54, 55].

• **Assignment Locks and Bars:** The **Locks and Bars tab** within the utility is vital for controlling flow based on specific constraints [56-62]. This includes locking a truck to a specific **Shovel** or **Dump**, or **Barring** it from specific shovels, dumping points, or material types [30, 43, 57, 63-74]. Improper use of these controls can lead to inefficiencies, hence the guidance that they should be limited to managing exceptions [57, 72, 75].

C. Performance and Maintenance Data

The utility manages key metrics influencing optimization decisions and maintenance scheduling.

• **Fuel Management:** The utility tracks **Fuel Left** and **Fuel Tank** size [30, 38, 66, 76-80]. This data is used for automatically monitoring fuel levels and optimally assigning trucks to fuel bays when they fall below configured thresholds [66, 81-89].

• **LP Truck Type:** The utility displays the **LP Truck Type** field, a crucial classification for the Linear Programming model based on truck size and historical loading rates, ensuring proper matching of trucks to shovels [54, 69, 90-92].

• **TKPH/TMPH:** Fields such as **Current TMPH/TKPH** and **Max TMPH/TKPH** track tire heat load, a critical safety and maintenance concern [78, 93-95]. High TKPH values influence assignment logic to route trucks on alternate, shorter paths to limit tire wear [88, 96-102].

• **Maintenance Timers:** Fields like **Retorque Trips Left** (tracking remaining cycles before tire re-tightening) and **Time Since Maint** are important for scheduling preventive maintenance assignments [103-106].

3. Data Integrity Maintenance and Troubleshooting

Controllers regularly use the Truck Utility to ensure the real-time model of the mine remains accurate, often correcting information originated from the field.

• **Cycle Integrity:** Manual intervention through the Truck Utility is often required when operators fail to interact correctly with the Jpanel, causing cycle errors like inaccurate load or spot times [107, 108]. If a truck’s communications fail, the Controller must manually record the truck's haul cycle activities using the **Next Action** button within the utility, moving the truck through phases like Arrive, Load, and Full [109-118].

• **Troubleshooting Tool:** The Truck Assistant is used to view **Assignment Context** messages, which explain why an assignment failed (e.g., due to restrictions, delays, or incompatibilities), allowing the Controller to find and resolve the root cause of the issue and ensure the truck receives a productive assignment [119-136].

• **Communications Health:** The utility includes tools like the **Ping** function to test the network connectivity of the truck, diagnosing problems that could compromise data transmission and real-time assignments [109, 137-140]. The **Field Comms** tab also displays raw network traffic (TMAC messages) for advanced troubleshooting of assignment and cycle issues [141-143].

--------------------------------------------------------------------------------

## Haul Truck Operational Status and Mine Dispatching

The operational **Status** of haul trucks, which includes states such as **Ready, Down, Delay, Standby, and Shiftchange**, is a critical real-time data point managed and monitored by Mine Controllers, primarily through utilities like the **TruckMod Utility** and the **Dispatch Utility**. A truck's status dictates its availability for automatic assignment and heavily impacts the accuracy of optimization calculations.

The general definition of equipment status falls within a mine's overall **Time Usage Model (TUM)**, which categorizes activities to track equipment availability and utilization for accounting and planning purposes [1-4].

Core Statuses and Operational Definitions

The core statuses recognized by the system (such as DISPATCH or Jmineops) for haul trucks are defined based on their availability for productive work:

1. **Ready (Operational Time):**

• **Definition:** Equipment functioning properly and available for production work [5, 6].

• **Impact:** **Ready** trucks are considered by the optimization and assignment calculations, meaning DISPATCH sends Ready trucks to Ready shovels and dumping points [5, 7]. The activity corresponding to a truck in a Ready status is defined as Operating Time [4]. When an operator logs on, they typically press the truck icon to change the status to **Assign**, which sends a Ready transaction to the central computer, signaling readiness to receive an assignment [8-10].

2. **Down (Downtime/Planned Loss/Breakdown Loss):**

• **Definition:** Equipment that is not functioning properly and is unavailable for use [5, 6, 11]. This status records time as Downtime, either due to a Planned Loss (scheduled maintenance downtime) or Breakdown Loss (unavailable due to a breakdown) [11].

• **Impact:** **Down** equipment is **not considered** in optimization and assignment calculations [5]. When a truck goes Down, DISPATCH stops including it in haulage fleet assignments and cancels its expected arrival times [12]. Down status changes, usually initiated by the operator entering a reason code, generate an **exception** that the dispatcher must manually accept for the status change to take effect [12-15].

3. **Delay (Operating Delay):**

• **Definition:** Equipment that is temporarily inactive (not producing) but is generally considered available or utilized for work [4, 6, 16]. Reasons include maintenance, operator delays, breaks (like lunch or crib), weather, or short moves [16-18]. Operational delays typically include activities like "fill water truck," "reposition," "crib," and "smoke" [19].

• **Impact:** Trucks on **Delay** status **are considered** in optimization calculations, and DISPATCH generally expects a delayed truck to eventually arrive at its destination, though not necessarily at the originally calculated time [7, 17]. The assignment engine evaluates delays; for instance, if a delay is short (e.g., 15 minutes for a loader), the system may still assign trucks to it, knowing the trucks will arrive after the delay ends [3, 20, 21]. However, if a maintenance delay is expected to be lengthy (e.g., over one hour), the dispatcher should manually change the status to **Down** so the equipment is removed from assignment calculations [22].

4. **Standby (Operating Standby):**

• **Definition:** Equipment is available for work but is temporarily unused, typically due to **lack of an operator ("No Operator")** or lack of work [4, 6, 11, 23, 24]. Standby is synonymous with Idle, Spare, and Available [24].

• **Impact:** **Standby** equipment is **not considered** in optimization calculations [5, 23]. Dispatchers should ask the departing operator to enter Standby upon logging off if the truck is not scheduled for the upcoming shift, preventing DISPATCH from including it in new shift calculations [25, 26].

5. **Shiftchange:**

• **Definition:** Status automatically applied when operators log off at the end of a shift, synonymous with **Tiedown** [24, 25, 27].

• **Impact:** DISPATCH **considers Shiftchange equipment** in its optimization and assignment calculations, anticipating quick operator logon. This prevents the system from having to reroute trucks or adjust its plan immediately if the next operator logs on late [7, 28]. Trucks with this status are typically parked in designated tiedown locations for operator exchange [29-32].

Management via TruckMod Utility

The **TruckMod Utility** (or Truck Utility/Truck Assistant) is the interface used by the Controller to manage a truck's status and ensure **data integrity** [33-36].

• **Checking Status:** The utility displays the current status and the **Last Status Reason** for the truck [33, 35, 37].

• **Changing Status:** Controllers can manually change a truck's status (Down, Ready, Standby, Delay) by right-clicking the **Truck Status** field and selecting a new status and reason code [38-41]. Status changes, except to Ready, generate exceptions that require manual acceptance unless the reason code is set to auto-accept [13].

• **Troubleshooting Assignments:** Checking a truck's status is a critical step in troubleshooting assignment failures. If an empty truck lacks an assignment, checking if the truck or the intended loading tool is on **Delay** is required [42-44].

• **Fuel Assignments:** Trucks nearing low fuel thresholds may receive a specific assignment and be placed in a **DELAY** status with an appropriate reason code (e.g., "Refuel" or "Fuel and service") [45, 46].

Maintaining accurate status is crucial because the entire optimization algorithm relies on this information; poor status management results in poor assignments, inaccurate production reports, and high costs per ton [17, 22, 47, 48].

--------------------------------------------------------------------------------

## Haul Truck Last and Next Action Systems

The concepts of **Last Action** and **Next Action** are crucial, real-time indicators for haul trucks, defining the current state of the truck within the haul cycle and the immediate expected event that must occur next to maintain operational flow. These fields are prominently featured in the **TruckMod Utility** (or Truck Utility/Truck Assistant) and related control screens like the **Dispatch Utility** and the **Action section** of the MineView application.

I. Purpose and Location of Last/Next Action

The primary role of tracking the Last and Next Actions is to allow the mine control system (like DISPATCH or Jmineops) to monitor and manage the progress of the truck through its cycle, ensuring maximum productivity and enabling timely intervention by the Mine Controller or the Assignment engine [1], [2].

These indicators are typically displayed in the **Truck Section** or **Action Section** of monitoring interfaces:

• **Last Action:** Displays the **last action performed by the truck** or recorded for it [3], [4], [5], [6], [7]. This field is read-only in the Dispatch Utility [4].

• **Next Action:** Displays the **next action that the truck will perform** or the next-expected action according to the system [3], [8], [9], [5], [10]. This action typically marks the end of the previous activity and the beginning of the next [8].

II. Examples of Truck Actions and the Haul Cycle

The haul cycle is a sequence of events, and the Next Action dictates the expected transition between these events [11], [12], [13]. A complete haul cycle involves phases like Traveling Empty to Dumping [14], [12], [15].

Common examples of actions recorded include:

|   |   |   |   |
|---|---|---|---|
|Previous Action (Last)|Current State (Status)|Next Expected Action|Trigger/Context|
|**Dumping** [16]|**Traveling Empty** [17], [18]|**Assign** or **Arrive** [19], [4]|Occurs after the truck bed is fully down, prompting the office to provide a new shovel assignment [18], [17].|
|**Assign** [5]|**Traveling Empty** [7]|**Arrive** [9]|The truck is traveling to its assigned loading destination [7].|
|**Arriving Empty** [18], [17]|**Waiting/Spotting** [20]|**First Bucket** [21]|The truck has arrived at the assigned shovel and is ready to begin loading [21].|
|**First Bucket** [22]|**Loading** [23]|**Full** [6]|Signals the start of the loading process after the first material bucket is placed [23], [22].|
|**Full** [24]|**Traveling Loaded** [25]|**Arrive Dump** [26]|The shovel operator signals loading is complete (or "loaded out" the truck), triggering a dump assignment [27], [28], [29].|

The system automatically generates an assignment to load (Assign) when the truck finishes dumping and transitions to the **Traveling Empty** state, though operators only see one assignment at the Traveling Empty stage to avoid confusion [30], [17].

III. Interacting with Last/Next Action via Utilities

The Mine Controller can view and interact with these actions through multiple interfaces:

1. **Truck Utility / Truck Assistant:** The `Next Action` field can be modified by selecting a new action from the menu [8], [9]. The **Action Section** specifically provides buttons and menus to change the unit's assignment (`Assign`) and instruct the system to perform the next expected action (`Next Action`) [31], [32].

2. **Dispatch Utility:** The `Last Action` and `Next Action` fields are part of the **Truck Section** [3]. The controller can also use the **Schedule Action Field** to perform actions for the operator (e.g., manually pressing _Arrive_, _Assign_, or _First Bucket_), which effectively overrides the expected next action [33], [34].

3. **Assignment Logic:** The occurrence of certain actions are critical triggers for the optimization engine (DP model). For example, **State Change** is an event that triggers the creation of a new assignment for a truck [35]. The system records when the truck presses Assign, Arrive, Load, or Full, generating transactions and corresponding exceptions that require dispatcher handling [27], [36], [37].

IV. Last Action/Next Action in Troubleshooting and Data Integrity

The tracking of Last/Next Actions is paramount for maintaining **data integrity** and troubleshooting assignments.

• **Anticipating Events:** The system constantly calculates the **Expected Time** for the truck to begin the Next Action [38], [5]. This expectation helps monitor late status, as a red label indicates the equipment is late for its next expected action [39].

• **Manual Intervention:** If an equipment operator fails to press a field computer button for the Next Action (e.g., they forget to press Load), the Mine Controller can use the Actions field in the utility to perform the action for the operator, often generating exceptions that must be accepted [40], [41].

• **Data Corruption:** Manual assignments or improper cycling (where operators fail to press the correct button) disrupt the expected cycle times and DP calculations, potentially leading to inaccurate travel times or zero spot/load times [42], [43]. For instance, if an operator does not take a machine off delay before resuming work, the system still assumes the machine is on delay, affecting the next action sequence and leading to misassigned trucks [44].

--------------------------------------------------------------------------------

## Mine Haul Truck Location and Destination Management

The sources emphasize that the **Location/Destination** of haul trucks is fundamental to the automated assignment, optimization, and reporting capabilities of mine management systems like DISPATCH and Jmineops. This information is meticulously tracked, continuously updated, and often manually managed by the Mine Controller using utilities such as the **TruckMod Utility** (or Truck Assistant).

I. Key Definitions: Location and Destination

The terms Location and Destination define where the truck is currently operating and where the truck is directed to go next in the haul cycle [1], [2], [3].

• **Location:** This field in the **TruckMod Utility** displays the truck's **current location**, which might be a pit, blast, or stockpile [4], [5], [6], [2]. The Location is dynamically tracked via GPS [7].

• **Next Loc/Destination:** This field, often viewed in the Truck Utility, displays the truck’s **next expected destination point** [4], [8], [1], [2]. An assignment is essentially a set of instructions telling the truck to travel from its current location to a given destination (Loading Tool, Processor/Dump, or Station) [9], [10].

II. Automated Tracking and Updates

The system relies heavily on continuous data transmission to accurately monitor the truck's location throughout its haul cycle:

1. **GPS Tracking:** Trucks fitted with GPS continuously report their position to the server [11]. This information is used to update the truck's Easting and Northing coordinates [7], [12].

2. **Activity State Transitions:** The system uses predefined cycle activities (such as **Traveling Empty**, **Hauling**, **Queued**, **Tipping**, etc.) which are triggered by the truck's actions (like moving past waypoints or detecting a bed-down sensor) to update its Location/Destination context [13], [14], [15], [16], [17].

3. **Virtual Beacons:** **Virtual beacons** associated with locations (End Point, Way Point, etc.) provide the framework for GPS tracking, triggering location updates, auto-arrivals, and dynamic reassignments when the truck enters their area of influence [18], [19], [20], [21], [22], [23].

4. **ETA Calculation:** The **Estimated Time of Arrival (ETA)** at the next assigned destination is continuously updated using the Best Path and average road segment speeds, which is vital for assignment logistics [24], [25], [26].

III. Location and Destination Management in TruckMod Utility

The **TruckMod Utility** (or Truck Assistant) gives the Mine Controller the ability to monitor, verify, and manually adjust the location and destination information for individual trucks [27], [28], [29], [30].

• **Reviewing Location Data:** The utility displays the **Location** field, reflecting the truck's current position, and the **Last GPS Location** and corresponding time, providing historical positional detail [4], [6], [7], [1], [31].

• **Relocating/Manual Update:** While location updates happen automatically, certain circumstances (like a communication failure or movement to an undesignated area like a workshop) require the Controller to **manually change the truck's location** using the TruckMod Utility [5], [32]. The Controller right-clicks the Location field and selects the truck's current workshop location or other destination [8], [33].

• **Next Location/Destination and Assignment:** The utility displays the **Destination** (the dump or shovel for the current assignment) and the **Assign To** field, which specifies the truck's next assignment [1], [2]. Controllers can manually override the automatic assignment by using the **Assign** button and selecting a new location from a menu of possible destinations (Dump, Crusher, Shovel, etc.) [34], [35], [36], [37].

• **Shift Change/Tiedown:** The utility is used to define a **Default Tie Down** station in the Shift Change tab, determining where the truck should travel at the end of a shift [38], [39]. The Location Utility also configures specific areas as designated **Tiedown Locations** [40], [41], [42].

IV. Location and Destination Impact on Optimization

Accurate location and destination data is non-negotiable for the optimization engine:

1. **Feasibility and Validity:** Accurate location data is critical for achieving a **"healthy road network"** [43], [44]. Incorrect location data, such as improperly set elevations for call points or using non-existent routes, can lead to invalid Best Path calculations, incorrect ETAs, and **poor assignments** (e.g., resulting in trucks queuing or loaders sitting idle) [45], [46].

2. **Misroute Detection:** The precise positional data is continuously compared to the assigned route (the "green line" on the operator's screen). If a truck travels off the assigned path by a configurable distance (default 100m), a **misroute event** is generated, making the assignment invalid and triggering a request for a new reassignment [47], [48], [49].

3. **Reassignment Triggers:** The location context (entering a reassignment callpoint, a shovel or dump changing status, or a change in material being dug) triggers the Joptimizer to recalculate whether the current assignment is still the **most productive/effective** [50], [51], [52], [53].

4. **Dump Assignment Hierarchy:** When a truck finishes loading and begins hauling, the system determines the dump assignment based on a hierarchy of locks, starting with the **Truck Dump Lock**, then the Shovel Dump Lock, and finally the Grade Dump Lock [54], [55], [56]. If no locks are present, the truck is assigned to the nearest Ready dump that accepts the material [57], [58]. This entire process relies on knowing the truck's loaded material and its precise current location relative to eligible dumps [55].

--------------------------------------------------------------------------------

## Mine Management Locks and Bars System Controls

The sources provide comprehensive details regarding **Locks and Bars** as critical restrictions used within mine management systems (like DISPATCH or Jmineops) to control the movement and assignment of **Haul Trucks**. These restrictions, configured primarily through utilities such as the **TruckMod Utility** and the **Shovel Utility** (or Loading Tool Assistant), dictate which equipment a truck is definitively _assigned to_ (Lock) or explicitly _prevented from accessing_ (Bar).

1. Definition and Purpose of Locks and Bars (Restrictions)

Locks and bars are defined collectively as **Restrictions**, which are rules that govern how trucks are forced to be **locked to, or barred from, one or more servers** (loading tools and processors/dumps) or mining blocks [1-5].

• **Purpose:** Restrictions are used to address **specific exceptions to the mining model** and should not be employed for general fleet management [6, 7]. Their role is to impose **constraints** that influence the optimization algorithms (Dynamic Programming, DP) to either include or reject equipment and locations when generating assignments [5, 8].

• **Impact on Optimization:** Excessive use of locks and bars is cautioned against because it can hinder the system's ability to maximize efficiency and **can lead to poor assignments, overtrucking, undertrucking, and lengthy queue times** at shovels [2, 6, 7, 9].

2. Management and Configuration via Utilities

The management of these assignment controls is typically done through dedicated windows accessed via the primary equipment utilities.

A. TruckMod Utility (Truck Utility/Truck Assistant)

The **TruckMod Utility** (or Truck Utility/Truck Assistant) allows the Mine Controller to apply assignment restrictions specific to an individual haul truck [6, 10, 11]. The **Locks and Bars tab** within the Truck Utility is the dedicated interface for this:

• **Truck-to-Shovel Lock:** This facility locks a specific truck to a designated shovel [6, 12-14]. The truck will only receive loading assignments to that specific shovel [15]. This is useful, for instance, if a truck requires a specific, relatively level route provided by paths linked to that shovel [16].

• **Truck-to-Dump Lock:** This locks a truck to a specific dumping location (dump, crusher, or stockpile) [12, 14, 15, 17]. This restriction confines the truck to hauling material that is **accepted at the dumping point** [15].

• **Truck-to-Region Lock:** This restricts a truck to a specific mine region, preventing it from receiving assignments to shovels or locations outside that defined region [12, 14, 18, 19].

• **Truck Bars (Shovel, Dump, Material):** The utility allows barring a truck from one or more shovels, dumping points, or material types [14, 19, 20]. Barring a truck from a shovel/dump means the system won't send the truck there; barring it from a material means it won't be assigned to a shovel digging that material [19].

B. Shovel Utility (Loading Tool Assistant)

The **Shovel Utility** (Loading Tool Assistant) allows setting restrictions that apply to all trucks using that specific loading tool [21].

• **Shovel-to-Dump Lock:** This locks the shovel to a specific dumping point (dump, crusher, or stockpile) [22-24]. **All trucks loaded by the shovel will be assigned to this locked dump** [24]. This is often used for material that must go to a specific location, such as in-pit dumping [22, 24].

• **Shovel-to-Region Lock:** Restricts the shovel to a specific region, which is mainly relevant in tramming scenarios [23].

• **Truck Locks/Bars on Shovel:** The Shovel Utility also manages lists of trucks explicitly locked to or barred from that particular shovel [23, 25].

3. Hierarchy and Behavior within the Dynamic Programming (DP) Model

The DP model, which handles real-time assignments, must enforce these restrictions, leading to a clear hierarchy, particularly regarding Dump Locks:

1. **Hard Lock on Grade:** A **Dump Lock on Grade with Hard Lock enabled** (configured on the **Shovel View** or **Grade View**) **overrides all other locks** and forces the truck carrying that specific grade to dump at the designated location (the **ONLY dump where this material is to be tipped**) [5, 26, 27].

2. **Truck Dump Lock:** A Dump Lock applied directly on the **Truck** (via the Truck View) disregards both the Dump Lock set on the Shovel and the standard Grade Lock [26, 28, 29].

3. **Shovel Dump Lock:** A Dump Lock set on the **Shovel** ensures trucks locked to that shovel haul material to that specified dump [26, 30].

4. **Grade Dump Lock (Standard):** A standard Grade Lock (set on the Grade View) ensures trucks carrying that material grade are assigned to any dump that accepts it [26, 31, 32].

If no lock is set, the truck loaded with a specific grade will be assigned to the **nearest Ready dump that accepts that grade** (material) [33-35].

**Important Behavioral Notes:**

• **Locks vs. Restrictions:** Locks constrain assignments to only the selected item (e.g., locking a truck to one shovel), while restrictions prevent assignments to the selected items, allowing all others (e.g., barring a truck from small shovels) [5].

• **DP Enforcement:** Since the Linear Programming (LP) model generally does **not consider truck-specific locks and bars** in calculating the optimal solution (LP Paths), applying locks and bars is a way for the dispatcher (via the DP model) to override optimization to achieve specific production goals, even though excessive application is discouraged [1, 2, 5, 8, 36-38].

• **Shovel Status Override:** If a shovel with trucks locked to it becomes **Down**, the system will **automatically unlock the trucks** and reassign them, preventing haulage cycles from being interrupted [25, 39].

• **Manual Assignments:** Manual assignments override both locks and restrictions, but they disrupt DP logic and cycle time calculations, emphasizing that this should be done sparingly, preferably as a "pre-assignment" when the truck is loaded [30, 38, 40-44].

--------------------------------------------------------------------------------

## LPTRUCK Types: Classification for Haulage Optimization

The sources provide a detailed explanation of **LPTRUCK Types** as a classification system fundamental to the operation of the Linear Programming (LP) and Dynamic Programming (DP) optimization models, particularly as they relate to matching haul trucks with loading units (shovels/excavators) for maximizing productivity. These types are managed and assigned to individual haul trucks through maintenance options accessible via the **TruckMod Utility** or the **Linear Programming Configuration Utility**.

Definition and Purpose of LPTRUCK Types

**LPTRUCK Types** represent a logical grouping of a mine's various truck types (e.g., specific manufacturer models like Wabco 170 or Cat 793) based on two primary characteristics: **truck size** and **average loading rates** achieved at different shovel types over an extended historical period (typically three months or more) [1], [2], [3], [4].

• **Role in Optimization:** The central purpose of LPTRUCK Types is to provide the LP model with the information needed to determine the **most efficient truck size** for every segment of the planned haulage cycle (LP Path) [5], [6], [7].

• **Assignment Logic:** LP allocates a specific LPTRUCK Type to each optimal LP Path found in its solution [8], [5]. Subsequently, the Dynamic Programming (DP) model preferentially attempts to enforce this decision by assigning actual trucks of that corresponding LPTRUCK Type to the associated shovel/path [9], [7], [4].

• **Limitation:** Modular Mining typically restricts the total number of LPTRUCK Types to six to reduce the complexity and computational load of LP calculations [10].

Configuration and Maintenance

LPTRUCK Types are a configuration input, requiring specialized setup and maintenance, typically managed by the Builder role or a Project Engineer [11], [12].

1. **System Tables:** LPTRUCK Types are defined in the system's background configuration, specifically within the `LPTRUCK_tbl` table in the `enum.c` file [2], [13], [14]. This table lists the LPTRUCK Types, their abbreviations, and status flags [14].

2. **Truck Size:** LP also relies on the **LPTRUCK Type size** (in tons), which is defined in the `lptrucksize_tbl` table and used when calculating the number of trucks required for an LP Path [15], [16], [2], [17], [18].

3. **Assignment via TruckMod Utility:** Although LPTRUCK Types are defined system-wide, individual haul trucks are assigned their specific LPTRUCK Type through the maintenance tools section of the **Linear Programming Configuration Utility** (specifically the **Assign LPTRUCK Types to Trucks** option) [1], [19], [6]. If a new truck is added, it must be assigned an LPTRUCK Type so the DP model can properly assign it along an LP Path [20].

4. **Dig Factors:** The efficiency match between a specific LPTRUCK Type and a specific shovel type (LPEXCAV Type) is quantified by the **dig factor** (a value between 0.0 and 1.0) stored in the `digfactor_tbl` table [13], [21].

• The LPTRUCK Type that records the **highest average loading rate** for a particular shovel type receives a dig factor of **1.0**, designating it as the best match [7], [22], [23], [21].

• An LPTRUCK Type with a dig factor of **0.0** is effectively **barred** from being assigned to that shovel type [24], [25], [26].

LPTRUCK Types and Productivity Objectives

The use of LPTRUCK Types is linked to a major global configuration parameter: the **Use Global Truck Productivity Objective** setting, found in the LP Global Parameters Utility [27], [28].

• **Objective Set to NO:** When this objective is set to **NO**, LP places a heavy emphasis on matching haulage resources to shovels primarily based on **truck-dependent loading rates**. This often results in bigger trucks being assigned to bigger shovels and smaller trucks to smaller shovels [27], [29], [28], [30], [31].

• **Objective Set to YES (Recommended):** When set to **YES** (the recommended setting), LP seeks to maximize overall truck productivity by routing haulage through the **most efficient paths** (shortest travel times) [27], [32], [28]. This overrides the strict size-matching rule, meaning a large LPTRUCK Type might be allocated to a medium-sized shovel if that shorter route offers greater overall productivity, even if it leads to a slight loss in shovel loading efficiency [32], [29], [33], [34].

In summary, the LPTRUCK Type serves as the optimization kernel's standard unit of haulage resource, allowing the system to model heterogeneous fleets efficiently and dynamically assign the most appropriate truck size to maximize material movement while adhering to complex operating constraints [8], [3], [35], [36], [37].

--------------------------------------------------------------------------------

## Haul Truck Speed Factor and Optimization Utility

The sources identify the **Truck Speed Factor** as a specific configurable parameter within the management of haul trucks, primarily via the **TruckMod Utility** (or Truck Utility), that plays a direct role in calculating travel times and estimating overall cycle durations within the optimization system. This factor is crucial for ensuring the accuracy of truck assignments and overall mine productivity.

Function and Location within the TruckMod Utility

The **Truck Speed Factor** field is a data point within the **TruckMod Utility** that adjusts how the system calculates travel time for an individual haul truck [1, 2].

• **System Tracking:** The field displays a value that deviates from the default of 1.00, indicating whether the specific truck is **slower or faster** relative to the fleet's average travel times [1, 3].

• **Default State (Off):** If the overall system parameter (`FACTORAVG`) related to the speed factor is set to **OFF** (0.00) in the `config.prv` file, the Truck Speed Factor field defaults to 1.00. This means the system uses the standard average speeds defined for the truck fleet when calculating travel times [1, 4].

• **Individualized Tracking (On):** If the system parameter is set to **On**, the system uses the truck’s **individual travel times** in its calculations [1, 3]. In this case, the displayed factor will reflect the actual performance variation from the average [1].

• **Numerical Range:** The factor accepts a number ranging from **0.0 to 4.0** [5].

Impact on Travel Time and Optimization

Accurate speed and travel time calculations are foundational to the effectiveness of the system's core optimization algorithms (Best Path, Linear Programming, and Dynamic Programming).

1. **Travel Time Calculation:** The speed factor interacts with the average speed values defined for the entire truck fleet (found in the `speed.prv` table within the `config.prv` file) [1, 4]. Accurate calculations of empty and loaded travel times (determined using road category, gradient, and the loaded/empty state) are essential inputs for generating assignments [6, 7].

2. **Assignment Decisions:** Errors or inaccuracies in the calculated travel times, which the Speed Factor influences, can lead the optimization system (DP model) to issue **poor assignments**, potentially resulting in trucks queuing, idle loaders, and higher costs per ton [8, 9].

3. **Causes of Deviation:** A truck registering a lower speed factor (indicating slower performance) can result from actual mechanical or operational issues, but also from user errors, such as **truck operators delaying in pressing field computer buttons** during the haul cycle [3].

In essence, the Truck Speed Factor provides a mechanism for the Mine Controller to fine-tune the expected performance of an individual truck, ensuring that the system's projections of **Estimated Time of Arrival (ETA)** and overall cycle times are realistic despite variations in fleet performance or operator efficiency [10, 11].

--------------------------------------------------------------------------------

## Haul Truck Tire Retorque Trip Management

The sources identify **Retorque Trips Left** as a specific, mechanical maintenance parameter tracked for haul trucks and managed through the **TruckMod Utility** and the associated monitoring systems. This value is directly related to tire maintenance following new tire installation and plays a role in scheduled maintenance assignments.

Definition and Purpose of Retorque Trips Left

The `Retorque Trips Left` field tracks the **number of haul cycles left before the truck requires a retorque assignment** for its tires [1].

• **Trigger for Assignment:** This field is activated when new tires are installed on a truck, which is detected when the truck changes its status to **Ready** after previously being **Down** for a "retorque" reason code [1].

• **Maintenance Procedure:** The need for a retorque trip stems from the standard tire-changing process, which involves installing new tires, running a few haul cycles to settle the tires into place, and then **returning to the shop to retorque (re-tighten) them** [1].

• **Source of Configuration:** The mine's `config.prv` file specifies the total number of allowable haul cycles before the tires must be retorqued [1].

Context in Haul Trucks and Maintenance Management

Within the broader management of haul trucks via the **TruckMod Utility** and the integrated maintenance framework, `Retorque Trips Left` is categorized under **Service History** information, alongside other critical operational data [2-4].

• **Location in Utilities:** The `Retorque Trips Left` (or **Trips till Retorque**) field is displayed within the **TruckMod Utility** and the **Maintenance tab** of the **Truck Utility** [1, 2, 5].

• **Data Tracking:** The Maintenance tab displays the number of retorques remaining out of the total required retorques for a tire [5]. For example, if the system requires a tire to be retorqued eight times, and it has already been done five times, the field displays a value of 3 [5, 6].

• **Scheduling Maintenance:** This metric informs the scheduling of **preventive maintenance** and specific service assignments. Trucks can be automatically assigned for maintenance (including retorque) when they surpass user-defined thresholds [7-9].

By monitoring this metric, the mine management system helps ensure tire maintenance procedures are followed precisely, which is crucial given that **tires are one of the top expenses for mine sites** (costing between 20,000–60,000 per tire) and **tire management is very important to job sustainability** [10, 11].

--------------------------------------------------------------------------------

## ShovelMod Utility: Mining Fleet Data Integrity and Control

The sources position **Shovels/Loaders** (often referred to as excavators or digging units) and the associated **ShovelMod Utility** (or Shovel Utility/Loading Tool Assistant) as central components for managing the mine model, production cycles, and ensuring **Data Integrity** within the fleet management system (like DISPATCH or Jmineops) [1-3]. The integrity of shovel data is paramount because inaccurate information leads directly to poor assignments, queuing, idle loaders, misdirected loads, and higher costs per ton [4-9].

The **ShovelMod Utility** is the specialized tool used by Controllers and Builders to configure, monitor, and update essential real-time and foundational data related to loading equipment [10-14].

1. Configuration and Location Management

The shovel's configuration and accurate location management are critical inputs to the Linear Programming (LP) and Dynamic Programming (DP) models that drive optimization [13, 15, 16].

• **Identity and Class:** The utility manages the shovel's ID, type (such as P&H 2800XP), bucket size (in tons or cubic yards), and its assigned crew number [14, 17, 18].

• **Location Tracking:** The shovel's location is automatically updated via **GPS positioning** every 30 seconds [19-21]. The utility displays the current **Location** (typically a bench, blast, or stockpile) [22]. If a shovel moves, the Controller must manually update its new location in the utility; failure to do so results in the road network being attached to the _incorrect destination_, negatively affecting truck assignments [23].

• **Mine Model Integration:** The shovel's current location and associated region and pit must be defined in the database. If a shovel moves to an undefined location, it may be **excluded from the LP solution for automatic assignment** [24-26].

• **Virtual Beacons and Auto-Arrive:** The utility sets the shovel's **Beacon ID** and **GPS Coverage Radius** (e.g., 200 feet or meters) [27, 28]. It also controls the **Auto-Arrive** feature, which, when enabled, automatically registers trucks arriving at the shovel, eliminating the need for manual operator input and avoiding assignment exceptions [28, 29].

2. Operational Status and Assignment Availability

The shovel's operational availability, set via the utility, is paramount for optimization [30-32].

• **Status Management:** The utility manages the shovel's **Status** (Ready, Down, Delay, Standby, Shiftchange) [10, 18]. Shovels must be set to **Ready** to be included in optimization and receive truck assignments [33, 34].

• **Assignment Control:** The **Assignable** checkbox in the Loading Tool Assistant directly controls whether the shovel receives automated assignments from trucks [32, 35]. A shovel set to **Down** or **Standby** is removed from the LP solution, meaning no automatic assignments are generated for it [36-38].

• **Handling Delays:** Delays (e.g., for short moves, cleanup) temporarily halt assignments, though trucks may still be assigned if the delay duration is short enough that the truck is expected to arrive just as the shovel comes off delay [39, 40]. The utility displays the **Last Status Reason** and time [19].

3. Productivity Metrics and Optimization Inputs

The accuracy of the shovel's measured performance metrics, managed through the utility, directly influences the cost calculations and haul flow rates of the LP/DP optimization layers.

• **Dig Rate (Tons/Hr):** The utility displays the calculated **Dig Rate** and often a **Max Dig Rate** [27, 41]. The Dig Rate, based on a rolling average of the last few truck loads, represents the shovel's capacity solely considering load time [42, 43]. Dispatchers can manually lower the Dig Rate in the utility (e.g., if moving from blasted material to cleanup) to prevent **overtrucking** by immediately informing DISPATCH of reduced capacity [44, 45].

• **Spotting Time (Sec):** The **Spotting Time, Sec** field displays the average time trucks spend backing into position for loading [27, 46]. This value is a rolling average, often based on the last four trucks [27, 46]. Spotting time is a critical input used by the LP model when calculating the **LP Dig Rate (Shovel Capacity)**, influencing the required haulage for the shovel circuit [47, 48].

• **Quick Spotting:** The **Quick Spotting?** field is marked (green X) if the shovel loads trucks from both sides, signaling DISPATCH **not to use spotting times** in haul cycle calculations for that shovel, aiming for efficiency in dual-loading scenarios [29].

4. Constraints and Production Planning

The ShovelMod Utility manages critical constraints that dictate assignment feasibility.

• **Material and Grade:** The shovel's current material type and associated grade must be correctly recorded, ensuring trucks are assigned only to dumping locations that accept that material [49, 50].

• **Dump Locks and Restrictions:** Controllers can set a **Dump Lock** to specify a fixed dumping point for all material loaded by that shovel [51-53]. They can also **Lock Trucks** to the shovel or **Bar Trucks** from the shovel, overriding automatic LP/DP assignments to enforce production objectives [54-56].

• **Shovel Priority/Coverage:** The utility displays **Ore Priority** and **Waste Priority** (High, Normal, Low) and the **Utilization** (% PROD CAP) [49, 57, 58]. These settings are vital in undertrucked situations, allowing the dispatcher to prioritize key shovels to ensure they receive resources preferentially over lower-ranked shovels [59-61].

--------------------------------------------------------------------------------

## Mine Management Shovel Location and Assignment Control

The **Location** of a shovel (or loader) is a core piece of real-time operational data managed within the mine management system, centrally configured and modified primarily through the **ShovelMod Utility** (or **Loading Tool Assistant**) and a corresponding location system like the **Location Utility** (LocMod Utility). This location data is crucial because it integrates the mobile shovel with the fixed mine infrastructure model, directly affecting assignments, productivity calculations, and system integrity.

1. Managing Shovel Location in the ShovelMod Utility

The **ShovelMod Utility** (also referred to as the Loading Tool Assistant or Shovel Utility) is the primary interface used by Controllers and Builders to monitor and update a shovel's spatial context and related operational details [1-4].

Key fields related to a shovel's position found in this utility include:

• **Location:** This field displays the shovel's current location, which is typically a **bench, blast, or stockpile** [5-7]. The utility requires manual updates if the shovel moves [8, 9].

• **Blast:** This specifies the **blast** where the shovel is currently digging [7].

• **Easting/Northing:** These coordinate fields (x and y coordinates) reflect the shovel's location, which is typically updated automatically by GPS every 30 seconds [10, 11].

• **Last Location/Next Location:** The utility may display the shovel's last known location, and for tramming excavators, the next expected destination point [12].

• **Destination:** For the Cat MineStar Client application, the Loading Tool Assistant (Shovel Utility equivalent) shows the shovel's current operating destination (where material is being sent, typically a dump or crusher) [13-15].

2. Importance of Accurate Location Data

Maintaining accurate location data for shovels is critical for overall system performance, optimization, and data integrity [16-20]:

1. **Assignment Feasibility:** If a shovel moves to a new location but the **Fleet Office is not updated**, the shovel’s road segments may remain attached to the incorrect destination [21]. This results in incorrect road network information, which negatively affects truck assignments [16, 17, 21].

2. **GPS Positioning:** Shovels equipped with GPS continuously report their position to the server, often updating Easting and Northing coordinates every 30 seconds [10, 11]. This real-time positioning data is essential for the automated assignment engine [19].

3. **Grade Management:** When a shovel changes location, the operator must inform the system and potentially update the grade and material being dug [22-24]. Accurate location information ensures the correct **grade ID numbers** and **material types** are associated with the shovel, which is necessary for accurate dump assignments and reporting [22, 25].

4. **LP Configuration:** The shovel's location is part of the **Pit Operational Configuration** used as an input variable for the **Linear Programming (LP) layer** in optimization [26-29]. If a shovel moves to a new location, and a region or pit is not defined for that location, the shovel may be excluded from the LP solution for automatic assignment [30-33].

3. Procedure for Moving a Shovel

When a shovel moves, the Controller is responsible for manually updating its location data to reflect the new position [8, 21, 34]. The typical sequence involves:

1. **Operator Action:** The shovel operator presses **Tram** (for a long move) or **Move** (for a short move) on the field computer to register a Delay status [8, 23]. An exception is generated in the office [8].

2. **Shovel Readiness:** Once the shovel reaches the new location, the operator radios the central office and presses **Ready** [8, 23]. If the material type is different, the operator enters the new **Material** type [23].

3. **Dispatcher Update (ShovelMod Utility):** The Controller opens the **Loading Tool Assistant** (ShovelMod Utility) [35, 36]. On the General tab, the Controller selects the browse button next to the **Destination** field to open the **Destination Chooser** window [34, 37].

4. **Selecting New Destination:** The Controller selects the new destination from the filtered list in the Destination Chooser [37]. The colors of the listed destinations indicate status, distance, and compatibility (e.g., Blue if near, Green if compatible, Red if not connected to road network) [36].

5. **Saving Changes:** The Controller clicks **Apply** or **Save** on the Loading Tool Assistant to save the changes, updating the shovel's location in the database [35, 38]. The Controller must also check if default materials, mining blocks, operating modes, and restrictions are correct for the new location [37, 38].

If the pit model is not maintained (i.e., if shovel location is not updated when the shovel moves), assignments will be negatively affected due to inaccurate road network information [21, 38].

--------------------------------------------------------------------------------

## MineStar Shovel Statuses and Truck Assignment Logic

The status of **Shovels/Loaders** (referred to collectively as excavators, loading units, or digging units) is a crucial real-time operational input managed by the Mine Controller, primarily through specialized tools like the **ShovelMod Utility** or **Loading Tool Assistant**. The status dictates the availability of the equipment and profoundly influences the overall mine optimization and assignment process [1-12].

In the context of the MineStar/DISPATCH system, four primary statuses are recognized for shovels: **Ready, Down, Delay, and Standby**, in addition to **Shiftchange** [6, 13].

Core Statuses and Their Operational Impact

The current status of a shovel determines whether the Linear Programming (LP) and Dynamic Programming (DP) models consider it for automatic truck assignments [4, 6, 14].

|   |   |   |
|---|---|---|
|Status|Definition and Meaning|Impact on Optimization and Assignments|
|**Ready**|The equipment is functional, available, and actively working in mine production [6, 7].|Shovels set to **Ready** are included in the LP solution and actively receive truck assignments from Ready trucks [6, 15, 16].|
|**Down**|The equipment is not functioning properly and is unavailable for use, constituting Downtime (either Planned Loss or Breakdown Loss) [6, 17-20].|Shovels set to **Down** are **not considered** in the LP assignment calculations [6, 9, 18, 21, 22]. When a shovel goes Down, the system immediately stops assigning trucks to it and may reroute trucks already en route unless manually managed [18, 23].|
|**Delay**|The equipment is temporarily inactive (e.g., for maintenance, breaks, blasting, short moves, or operator delays) but is generally considered in assignment calculations [6, 17, 19, 24, 25].|**DISPATCH does not assign trucks** to a shovel currently on **Delay** status [25]. However, the system still expects delayed trucks to arrive at their destinations, possibly factoring in the anticipated end time of the delay [19, 25-27]. If a delay is expected to be long (e.g., more than one hour), the status should be manually changed to **Down** so the system ceases considering the equipment in assignments [19].|
|**Standby**|The equipment is available for work but is temporarily unused, typically due to lack of an operator ("No Operator") or lack of work [6, 17, 28, 29].|Shovels on **Standby** are **not considered** in the LP optimization and assignment calculations [6, 9, 22, 29]. The status should be set to Standby for equipment not scheduled for the upcoming shift [7, 28].|
|**Shiftchange**|This status is applied automatically when operators log off at the end of a shift where production breaks occur (synonymous with Tiedown) [28, 30].|Shovels on **Shiftchange** status **are considered** in optimization calculations, anticipating quick logon by the next shift's operators [31].|

Status Management via ShovelMod Utility and Assistants

The **ShovelMod Utility** (or **Loading Tool Assistant**) is the primary interface used by the Mine Controller to monitor and adjust a shovel's status and related parameters [2, 32-35].

1. **Checking and Changing Statuses:** Controllers routinely check shovel statuses before and during a shift to ensure that required equipment is **Ready** or **Shiftchange** and that non-required equipment is placed on **Down** or **Standby** [7, 36]. Status can be changed by right-clicking the status field in utilities like the **Dispatch Utility** or **ShovelMod Utility** and selecting the new status along with a reason code [8, 32, 37-41].

2. **Reasons and Documentation:** Status changes (Down, Delay, Standby) typically generate exceptions requiring dispatcher action and reason code input [6, 20, 40, 42-44]. Reason codes categorize the event (e.g., "GENERAL MECHANICAL" for Down) and can be configured in the **Reasons Utility** [44-47]. It is critical that delays and status changes accurately represent the event for proper reporting and time accountability [17, 48-54].

3. **Assignment Delay Configuration:** Specific delay types can be configured as **Assignment Delays** [55, 56].

• If a loading tool is on delay, and the global setting allows assignments to delayed loaders _and_ the delay type is configured as an **Assignment Delay**, trucks will generally not be assigned to the shovel until shortly before the scheduled end of the delay (e.g., 5 minutes or 300 seconds) [48, 57].

• If the delay is _not_ configured as an Assignment Delay, trucks may continue to be assigned throughout the delay period [48, 57].

4. **LP Solution Exclusion/Inclusion:** Correct status settings are vital for **Data Integrity** [58, 59]. If a shovel is unavailable for assignment (Down or Standby), it is removed from the LP production plan [3, 60]. The **LP Shovel Configuration Analyzer** explicitly warns if a shovel is excluded from the LP solution because it is **Down** or **Spare** (Standby) [22, 61-63].

The concept of status is integrated into assignment troubleshooting; for instance, checking if a loading tool is on **Delay** is a fundamental step when an empty truck fails to receive an assignment [21, 64-67]. Incorrect status management can lead to major operational disruptions, including truck queuing, idle loaders, and inaccurate performance metrics [51, 52, 58, 68, 69].

--------------------------------------------------------------------------------

## Mine Optimization through Shovel Dig Rate and Capacity

The sources extensively define and discuss **Dig Rate (Tons/Hr)** as a critical performance metric for shovels and loaders, managed and utilized by mine management systems like DISPATCH and Jmineops to optimize truck assignments within the broader context of the **ShovelMod Utility** and the Linear Programming (LP) layer.

Definition and Calculation of Dig Rate

The **Dig Rate** is fundamentally a measure of a shovel's maximum theoretical productivity.

• **Dig Rate (Digging Rate)** is defined as the **amount in tons/hour that a shovel could load into trucks** in one hour, considering **loading time only** [1, 2].

• It does **NOT** take into account non-productive times such as hang time, wait time, spotting time, delays, breaks, or downtime [1, 2].

• The system calculates the Dig Rate using the shovel's loading rate and the defined Standard Truck Size [3]. For example, if a shovel has a 3-minute loading time, it could load 20 trucks in an hour (60 minutes divided by 3 minutes). If the standard truck size is 225 tonnes, the Dig Rate is calculated as 4,500 tonnes per hour (20 trucks multiplied by 225 tonnes) [3].

In the **ShovelMod Utility** or **Dispatch Utility**, the Dig Rate is displayed in tons per hour (T/H) [4-7].

Context in ShovelMod Utility

The **ShovelMod Utility** (or Shovel Utility) is the primary interface where shovel parameters, including those related to digging performance, are managed:

• **Display and Source:** The `Dig Rate` field displays the shovel's **current digging rate** in tons per hour [5, 6]. This rate is derived from the system's ongoing calculations [5, 6].

• **Dynamic Updating:** The Dig Rate value is dynamic; it **changes during the shift** as DISPATCH calculates the actual dig rate using a **moving average of the last** n **(typically four) trucks loaded at the shovel** [5, 6, 8].

• **Maximum Dig Rate:** The utility also features a **Max Dig Rate** field [9, 10]. This maximum value acts as a ceiling; DISPATCH will **not allow the shovel's current dig rate to exceed this value** [9, 10].

• **Manual Adjustment:** Dispatchers can **manually change a shovel’s digging rate** through the utility if the shovel moves to a drastically different mining face (e.g., from blasted material to cleanup), where the digging capacity decreases [11, 12]. Lowering the digging rate in the utility immediately signals DISPATCH to respond to the new rate, preventing the system from **overtrucking the shovel** [11].

Role in Optimization and LP Calculations

The Dig Rate serves as a foundation for determining shovel utilization and assignment logistics within the **Linear Programming (LP) layer** of Joptimizer/DISPATCH:

1. **Capacity Constraint:** The Dig Rate (or digging rate) is incorporated into the LP solution as a constraint, specifying that the total material flow (paths) leaving a shovel must be **less than or equal to the shovel's maximum dig rate** [13, 14].

2. **LP Dig Rate (Shovel Capacity):** LP relies on a slightly refined metric, often termed **LP Dig Rate** or **Shovel Capacity** [15, 16]. This metric incorporates the actual Dig Rate (based on load time) along with the **average truck spotting times** at the shovel [15-17].textShovelCapacity(LPDigRate)=fractextStandardTruckSizetext(StandardTruckSize/textDigRate)+(textSpotTime/3600)The calculation of Shovel Capacity (LP Dig Rate) is derived from the average dig rate and spot time, reflecting the rate at which the shovel could load 'standard trucks' [15, 16].

3. **Required Haulage:** The calculated Shovel Capacity/LP Dig Rate is used, along with the **Standard Truck Size** and **Total Cycle Time**, to determine the **Trucks Required** (or Required Haulage) for a specific circuit [18-20]. This value is essential for the Dynamic Programming layer to decide which shovels are the "neediest" and thus prioritize truck assignments [21, 22].

4. **Productivity Comparison:** The Dig Rate is useful for **comparing Loading Tool operators** or assessing the quality of material excavation (e.g., if material has been blasted well), as it represents a theoretical maximum [23].

In essence, while the Dig Rate indicates the raw speed of loading, the operational efficiency relied upon by the optimization system is the **LP Dig Rate/Shovel Capacity**, which integrates both loading time and the time trucks spend positioning (**Spotting Time**) [15].

--------------------------------------------------------------------------------

## Mining Shovel Priority and Allocation Optimization

The sources provide significant detail regarding **Ore/Waste Priority** within the context of managing shovels/loaders, particularly how these priorities are configured using tools like the **ShovelMod Utility** and the **Shovel Priority/Coverage Utility**, and how they influence the **Linear Programming (LP)** layer of the optimization system.

These priorities are crucial for ensuring that mining operations meet their targets, especially during periods when hauling resources (trucks) are limited (undertrucked situations).

Global Material Priority Schemes

The system utilizes "global material priority schemes" to establish a preference hierarchy between ore and waste materials across the entire fleet [1-9]. These global priorities are managed through the **LP Global Parameters Utility** or the **Shovel Priority/Coverage Utility** [2, 6, 10-13].

The three primary global priority schemes are:

1. **Ore Equals Waste (Ore Equals Waste):** Under this scheme, comparable priorities are equivalent regardless of the material type (ore or waste), meaning a High ore priority and a High waste priority are treated the same [3, 6, 7, 10, 14-16]. This is the default setting recommended when sufficient trucks are available to cover all shovels at their production capacities, as it simplifies the LP model [13, 14, 17].

2. **Ore Above Waste (Ore Above Waste):** Ore shovels receive higher prioritization than waste shovels. The resulting hierarchy is typically: High ore, Normal ore, Low ore, followed by High waste, Normal waste, and Low waste priorities [3, 6, 10, 15, 18-20]. Shovels with a Low waste priority are the first to starve in undertrucked situations under this scheme [18, 19].

3. **Waste Above Ore (Waste Above Ore):** Waste shovels receive higher prioritization than ore shovels, resulting in a hierarchy of High waste, Normal waste, Low waste, followed by High ore, Normal ore, and Low ore [3, 6, 10, 15, 19-21]. Shovels with a Low ore priority are the first to starve [18, 19].

Individual Shovel Priorities (Low, Normal, High)

In addition to the global material scheme, individual loading tools are assigned a priority level—**High, Normal, or Low**—for both **ore** and **waste** material [1, 6, 8, 22-36]. These individual priorities influence how the LP model allocates resources when the shovel is digging that specific material type [4, 8, 27, 37].

• **High Priority** shovels are favored and are the last to starve in undertrucked situations [2, 8, 30, 35, 38-40]. Dispatchers often assign High priority to important long-haul shovels or key ore shovels to ensure adequate truck coverage [38, 39]. Setting a shovel priority to High can result in trucks being **overtrucked** to ensure minimal hang time [40-42].

• **Normal Priority** sits between High and Low in the hierarchy [2, 30].

• **Low Priority** shovels are the first to starve when trucks are scarce [2, 8, 30, 35, 43]. Setting a shovel to Low priority may be counterproductive; sometimes disabling the shovel and manually locking a truck to it is preferred for specific production needs [43].

• A shovel assigned a **Disabled** status is removed from the LP solution for automatic assignment, meaning trucks will only be assigned to it if they are explicitly locked or manually assigned to it [43-50].

These priorities combine with the global scheme to create a specific **Priority Level** (typically 1–6 or 1–10) for each shovel [1, 13, 16, 21, 35, 51, 52]. LP then ranks shovels based on this level, allocating haulage feed rates to the highest ranking shovels first [53-57].

Configuration via ShovelMod Utility and Optimization

The priority settings are crucial for the optimization model, particularly when the `Allow UNUSED Shovels in Undertrucked Situations (LPMATCHTRUCKS)` option is set to **YES** (which allows LP to prioritize coverage at the expense of lower-ranked shovels) [29, 39, 58-64].

In addition to setting priorities, the **ShovelMod Utility** (or **Shovel Utility**) allows configuration of **Percent Coverage** (or **Utilization**) [32, 65, 66].

• A shovel's **Production Capacity** (% PROD CAP) is the maximum percentage (0%–150%) that the LP model is supposed to cover the shovel’s loading rate [28, 41, 66-70].

• Priorities influence this percentage; for instance, a High priority typically translates to **125% capacity**, aiming for minimal idle time [40-42].

• If the Actual LP Coverage falls below the desired capacity (starved shovel), the Mine Controller must take action, such as adjusting priorities or ensuring more trucks are made available [66, 71-73].

The Shovel Utility often displays the individual **Ore Priority** and **Waste Priority** fields, where the dispatcher can select High, Normal, or Low [23, 24, 32, 33, 65]. This direct control ensures that the LP system models material movement according to the shift's production plan, minimizing instances where trucks are misassigned or shovels are unnecessarily idle due to resource constraints [26, 35, 38].

--------------------------------------------------------------------------------

## Truck Cycle Spotting Time: Definition, Calculation, and Optimization

The sources define **Spotting Time** as a critical component of the truck cycle that measures the time a haul truck spends positioning itself for loading or unloading, specifically in relation to **Shovels/Loaders**. This metric is vital for calculating cycle durations, optimizing assignments, and gauging the efficiency of loading operations. The configuration of how this time is recorded is managed within the shovel/loader management utilities, such as the **ShovelMod Utility**.

Definition and Calculation of Spotting Time

Spotting time is explicitly defined as **the average number of seconds it takes for a truck to back into position for loading** at a shovel [1].

• **Measurement Interval:** For trucks arriving at a shovel, spotting time is generally the interval between **the shovel operator pressing FULL (to load out a truck)** and the **next truck driver pressing LOAD** [1-3].

• **Alternative Calculation:** If no other trucks are in the queue, spotting time is the interval between the truck’s registered **arrival at the shovel and the moment the truck operator presses Load** [1]. If the truck must wait in a queue, spotting time is the interval between the **completion of the previous truck’s load** (shovel pressed Full) and the **start of the current truck’s load** (current truck operator presses Load) [4].

• **Movement Context:** Spotting time occurs **when reversing until loaded or the last movement out of queue** [5, 6]. This activity occurs in the office system and **will not appear on the operator's screen** [5, 6].

• **Calculation Method:** The system calculates the Spotting Time value as a **rolling average of the last** n **(typically four) trucks** that loaded at the shovel [1, 7-18].

Importance in Assignment and Optimization

Spotting time is a critical input used by the optimization models to maximize productivity and minimize costs:

1. **Cycle Time Calculation:** Spotting time is summed with loading time, dumping time, and travel time to determine the **Total Cycle Time** [9]. This comprehensive time calculation is essential for the Linear Programming (LP) and Dynamic Programming (DP) algorithms when setting haulage assignments [19, 20].

2. **Productivity Metric:** Spotting time is factored into the calculation of **Shovel Tons per Hour** (productivity metric) alongside total load time and total truck payload [21]. Minimizing this time is key to improving productivity [22].

3. **LP Calculations:** Spotting time is a direct factor in the **LP Dig Rate** calculation (or Shovel Capacity), which is the rate at which the shovel could load standard trucks given average load and spotting time [23-25]. Lower spotting times result in greater calculated capacity for the shovel, making the route more attractive to the optimization algorithm [17, 26].

4. **Assignment Decision Inputs:** Spotting time is one of the **State Times Used for Assignment Calculations**, specifically designated as a **Design time configured in the Loader Class and Shovel Class Editors** [27].

Configuration within the ShovelMod Utility

The **ShovelMod Utility** (or Shovel Utility) is the primary interface used to set or modify parameters related to shovel performance, including Spotting Time:

• **Manual Override:** The utility displays the **Spotting Time, Sec** field where the shovel's average spotting time, in seconds, is managed [1, 28, 29]. This allows dispatchers to set the default expected spotting time.

• **"Quick Spotting" Feature:** Shovels capable of loading trucks from both the left and right sides (dual-sided loading) can be flagged for **Quick Spotting** [30]. When this feature is enabled, the system **will not use spotting times** when calculating haulage cycle times involving that shovel, streamlining the cycle time calculation for efficient dual-loading operations [30, 31].

• **Spotting Distance:** The distance within which a truck starts backing is governed by the **Spotting Radius/Distance**, one of the configurable shovel radii inputs defined in Jmineops [32, 33].

The reliance on accurate spotting time emphasizes that **every second counts** in optimization, as a gain of just one second per cycle can equate to substantial additional production over a year [34-36]. Incorrect spotting times contribute to poor decision-making by the fleet management system, potentially resulting in unnecessary truck queuing or idle loaders [37].

--------------------------------------------------------------------------------

## Quick Spotting Feature in Haul Cycle Optimization

The sources mention **Quick Spotting** as a configurable feature specifically associated with managing loading tools (shovels, excavators, and loaders) through utilities like the **ShovelMod Utility** within the context of optimizing the haul cycle.

Definition and Purpose of Quick Spotting

The primary definition and purpose of the Quick Spotting feature is related to how the system calculates the time spent by a truck positioning itself for loading:

• **Dual-Sided Loading:** Quick Spotting is enabled for any shovel that is loading trucks **from both its left and right sides** (dual-sided loading) [1].

• **Time Calculation Impact:** When the **Quick Spotting?** field is marked (set to **YES**), the system **will not use spotting times** when calculating haulage cycle times involving that shovel [1, 2].

The elimination of spotting time calculation when loading occurs from both sides suggests a method to reduce overall cycle time and maximize efficiency in dual-loading setups, as the instantaneous transfer of trucks between the loading and waiting states streamlines the process.

Configuration within the ShovelMod Utility

The **ShovelMod Utility** is the tool used by dispatchers and builders to manage the configuration and operational status of loading units [3-5].

• **Field Presence:** The `ShovelMod Utility` window includes a checkbox labeled **Quick Spotting?** [1, 2, 6].

• **Enabling the Feature:** Marking this field (**green X**) sets the feature to `YES`, indicating that the shovel loads trucks from both its left and right sides [1].

Context in the Haul Cycle and Efficiency

The concept of Quick Spotting is directly related to optimizing the detailed components of the loading phase of the truck cycle:

1. **Spotting Time:** Spotting time is generally defined as the interval between the shovel operator pressing **FULL** (to load out a truck) and the next truck driver pressing **LOAD**, or the average number of seconds it takes for a truck to **back into position** for loading [7-9]. This value is normally calculated as a rolling average based on the last four trucks loaded at the shovel [7-10].

2. **Dual-Sided Spotting Logic:** In a **dual-sided loading situation**, if a second truck has arrived and spotted, it enters a **Waiting to Load** state for a configurable period (default 5 seconds) until the first truck is fully loaded and leaves the area, allowing the second truck's state to change to **Loading** [11-13]. Quick Spotting overrides the tracking of the standard spotting activity, likely because the efficiency gained from dual-sided loading makes the separate measurement of individual truck spotting times redundant or negligible for overall cycle optimization.

3. **Optimization Impact:** The core objective of the assignment engine (DP model) is to minimize individual cycle component times to increase the overall number of cycles and move more tonnage [14-16]. By enabling Quick Spotting, the system acknowledges that the shovel is inherently performing the spotting and positioning maneuvers efficiently, resulting in optimized loading cycle times for assignment calculations.

If the Quick Spotting field is marked **YES**, the system effectively chooses not to use the empirically measured spotting time in its calculation of cycle duration for assignments [1, 7].

--------------------------------------------------------------------------------

## Mine Management Auto-Arrive Functionality and GPS Beacons

The concept of **Auto-Arrive** is a configuration feature used in mine management systems like DISPATCH and Jmineops, specifically managed through the **ShovelMod Utility** (for shovels/loaders) and the **Location Utility** (for fixed locations/beacons). It relates directly to the automation of the haul cycle, reliance on **GPS virtual beacons**, and improving the accuracy of recorded cycle times.

Defining Auto-Arrive for Shovels/Loaders

For shovels (which include excavators and loaders) managed via the **ShovelMod Utility** (or Shovel Utility), the Auto-Arrive setting dictates whether trucks are automatically recorded as having arrived at the shovel location [1, 2].

• **Functionality:** When the **Auto-Arrive?** check box is marked (set to **Yes**) in the Shovel Utility's Details tab, the system automatically registers that a truck has arrived at the selected shovel when the truck enters the shovel area [2].

• **Operator Input:** If Auto-Arrive is enabled, the truck operator **does not need to press "Arrive"** on their field computer [1-3].

• **Exception Handling:** If the Auto-Arrive feature is enabled and a truck operator mistakenly presses the "Arrive" button upon entering the shovel area, an **exception** will appear on the Exceptions window [1, 2].

• **Configuration:** The Auto-Arrive setting, along with the **Beacon ID** and **Beacon Radius** (in feet or meters) of the GPS virtual beacon at the shovel location, is accessible via the **Details tab** in the Properties section of the **Shovel Utility** [2, 4, 5].

Context in GPS Beacons and Activity Detection

Auto-Arrive functionality is dependent on the accurate configuration of **virtual beacons**, typically of the **End Point** or **Load/Unload** type, set around the shovel location [6, 7].

• **Trigger Mechanism:** The system detects when a truck **enters the beacon’s circle of influence** (defined by the radius) and then **slows down** to approximately 5 miles per hour (or 10 kph, as per Jmineops references). This combination of position and speed reduction triggers the auto-arrival of the truck [7-9].

• **Road Network Integrity:** The beacon settings (including Auto-Arrive) rely on the shovel having accurate GPS infrastructure and a defined **virtual beacon** [10]. The correct beacon radius is crucial; if it is too small, the beacon may not track all traffic. If it is too large, it may capture unrelated traffic [11-13].

• **Activity Cycle:** For the truck's activity cycle, Auto-Arrive ensures the truck transitions smoothly into the **Waiting at shovel** or **Spotting** activity without requiring manual operator intervention [14, 15].

Operational Impact of Auto-Arrive

The proper use of the Auto-Arrive feature contributes directly to data integrity and optimization efficiency [16]:

• **Cycle Time Accuracy:** By automating the arrival detection, Auto-Arrive helps ensure that accurate load and haul data is captured for cycle time calculation [17]. Without proper functioning, the system may register a truck as having missed the arrival action, leading to inaccurate cycle component times [18, 19].

• **Workflow Efficiency:** The feature minimizes operator steps (eliminating the need to press 'Arrive') [2, 3].

• **Configuration Consistency:** Auto-Arrive status is also set for other locations, such as dumps and shops, via the **Location Utility** (or LocMod Utility) to ensure system-wide consistency in automated location arrival tracking [3, 6, 20, 21].

In short, Auto-Arrive is a core element in the assignment system's reliance on accurate, real-time positional data, enabling automated truck cycle progression based on proximity to the shovel's defined GPS area [22].

--------------------------------------------------------------------------------

## Mine Location Utility and Data Integrity

The sources portray **Locations**, managed primarily through the **Location Utility** (also referred to as LocMod Utility, Dump Utility, or Dispatch Utility), as a fundamental category of mine information components critical for the proper functioning of the mine management system and the preservation of **Data Integrity**. These utilities allow for the definition, configuration, and control of all fixed physical points within the mine model, which directly impacts optimization algorithms, operational assignments, and reporting accuracy.

1. Essential Functions and Utility Management

The Location Utility serves as the central control for creating, editing, and viewing all non-mobile entities within the mine model [1-4].

• **Core Management Tasks:** The utility enables dispatchers and builders to **add and delete locations** and display or change their associated information [1, 3, 5-7].

• **Hierarchical Structure:** Locations are organized hierarchically: **Pits** (most general), **Regions** (middle level), and then specific operational locations (most specific) like shops, dumps, crushers, benches, blasts, and callpoints [8]. This structure must be followed when adding new locations, proceeding from the general to the specific [9].

• **Coordinate Data:** For locations other than pits and regions, the utility stores **Easting, Northing, and Elevation** coordinates [10]. Accurate elevation (Z coordinate) data is critical because it is used to calculate the road segment gradient, impacting the calculation of the Best Path (BP) and Linear Programming (LP) assignments [11-13].

• **GPS Integration:** Locations are often linked to **virtual beacons** (circular or polygon shaped) defined by a **Beacon ID** and **Radius** [14-17]. These beacons are used for equipment tracking, auto-arrival, determining travel times, and triggering reassignments [16, 18, 19].

2. Location Types and Operational Status

Locations serve various functions, and their operational configuration dictates how they interact with the assignment engine [20-22].

Key Location Types Defined:

• **Dumps, Crushers, and Stockpiles (Tipping Points/Processors):** These locations require configuration regarding what **Materials** they accept [23-26], their **Target Feed (tons/Hr)** [24, 27, 28], and their maximum **Dump Capacity (tons/Hr)** [29-31]. They are crucial constraints for the optimization system [11, 32].

• **Shops:** These include maintenance shops, fueling areas (fuel bays), crib huts, and offices [8, 33, 34]. For shops, the utility may configure the **Max Trucks at this Loc** (maximum number of trucks allowed at the shop) and designate if the shop is a **Tiedown Location** [30, 33, 35, 36].

• **Blasts and Benches:** These are excavation points defined by geographical coordinates [8, 37]. Blasts contain specific **Grades** (material qualities) that link to dump compatibility and blending requirements [4, 38].

• **Callpoints:** These serve as key nodes in the road network, marking the start and end of road segments, and must have accurate **Elevation** set for proper road gradient calculation [11, 12, 39, 40]. Strategic callpoints can be marked with a **Reassignment Flag** to trigger checks by Joptimiser during travel [40, 41].

Location Status (Ready, Down, Delay, Standby, Shiftchange):

The **Status** field is used to control the operational availability of locations [42-44].

• **Ready:** The location functions properly and is included in optimization and assignment calculations [20, 45].

• **Down / Standby:** The location is unavailable (due to malfunction or no work/operator). DISPATCH/Jmineops **does not consider** Down or Standby locations in assignments [20, 42, 46]. Non-required dumping points should be set to Down or Standby prior to shift start [47].

• **Delay:** The location is temporarily inactive but is still included in calculations [48, 49].

• **Shiftchange (Tiedown/Parkup):** Equipment statuses automatically transition to Shiftchange when operators log off after a production break [33, 46, 50, 51]. The system considers this equipment in assignment calculations, anticipating future logons [50].

3. Importance to Data Integrity and Optimization

The rigorous definition and maintenance of locations is inseparable from the goal of achieving high **Data Integrity** and efficient optimization [52-58].

• **Optimal Assignments:** Accurate location data is critical because the fleet management system relies on its knowledge of the mine model to make the best decisions [52, 53, 55, 59]. Inaccurate location data (e.g., incorrect beacon radii, elevations, or restricted materials) leads to poor assignments, which results in problems like trucks queuing, idle loaders, misdirected loads, and higher costs per ton [52, 53, 55, 57].

• **LP Feasibility and Constraints:** The location configurations directly serve as **constraints** for the Linear Programming (LP) layer of the optimization system [60, 61]. Incorrect status settings, material acceptance lists, or grade restrictions prevent LP from creating **feasible haulage routes** [62-64].

• **Impact of Elevation:** Setting the Elevation (Z coordinate) for callpoints and locations is crucial, as the resulting **road gradient** information is used to accurately calculate empty and loaded travel times for the Best Path (BP) and Dynamic Programming (DP) models [11-13, 65]. Corrupt or incorrect elevation data leads to corrupted average speeds and inaccurate ETA calculations [66-69].

• **Continuous Monitoring:** Since the mine environment is dynamic (roads and ramps change shift-to-shift), dispatchers must continuously monitor and update location and road networks in utilities like the Location Utility to maintain system accuracy [70, 71].

--------------------------------------------------------------------------------

## Mine Management System Location Types and Utility

The sources identify various **location types** within a mine management system (such as DISPATCH or Jmineops) that serve specific functional roles in the mining operation and are managed centrally through utilities like the **Location Utility** or **LocMod Utility**. These locations form the structural foundation of the mine model and are critical inputs for optimization algorithms, directly impacting **Data Integrity**.

The key specific location types mentioned include:

1. Dumps, Stockpiles, and Crushers (Tipping Points/Processors)

Dumps, stockpiles, and crushers are collectively categorized as **dumping locations** or **processors** (sinks) where material is transported and disposed of or processed [1-6]. These locations are critical because they accept specific material types and impose constraints on throughput, directly affecting truck assignments [4, 7, 8].

• **Material Acceptance and Grade:** Dumping locations must be configured to accept one or more enumerated material types [8-10]. The system checks the grade(s) being dug by a shovel and configures paths only to dumps that accept that material [8-10]. If a dump location does not accept the material being loaded by a shovel, the system will not assign trucks from that shovel to the dump [11].

• **Capacity and Flow Control:** These locations utilize metrics like **Target Feed (Tons/Hr)** and **Dump Capacity (Tons/Hr)** to manage throughput [2, 12-17].

    ◦ **Crushers** generally have their **Nominal Rate** (Dump Capacity) set to the **engineering maximum rate**, and adjusting the **Target Feed Rate** is used to manage truck flow [17-19].

    ◦ **Dumps and Stockpiles** often have their Capacity set to "No Limit" or 5000 tph, and their Target Feed set to "No Target," as they typically accept all material until full, unless flow restriction is necessary (e.g., to match bulldozer capacity) [13, 20].

• **Queuing Limits:** Dump and crusher locations can also have a **Max Trucks at this Loc** or **Queue Capacity** defined, which limits the number of trucks allowed at the location simultaneously (in Queuing or Backing activity) to prevent bottlenecks [16, 21, 22].

• **Blending:** Crushers and stockpiles can have **blending parameters** applied via the **BlendMod Utility** to control the quality and composition of material delivered, enforcing constraints that the Linear Programming (LP) layer must satisfy when making assignments [23-30].

2. Shops (Workshops, Fuel Bays, etc.)

**Shops** refer to locations that provide services or maintenance for equipment [3, 31, 32]. These are crucial for scheduling and maintaining equipment health.

• **Types of Shops:** Shops include workshops/maintenance shops, fueling areas (fuel stands/docks/bays), water stands/truck shops, tire shops, crib huts, and offices [28, 32-34].

• **Status and Eligibility:** Like other locations, shops have statuses (**Ready, Delay, Down, Standby**). A shop with a **Ready** or **Delay** status is eligible for assignments (e.g., fuel or maintenance assignments), whereas **Standby** or **Down** shops are ineligible [1, 35].

• **Queue Control:** Shops utilize the **Max Trucks at this Loc** field to limit the maximum number of trucks simultaneously allowed for service or parking [16, 21].

3. Blasts and Benches (Excavation Sources)

**Blasts** and **Benches** serve as the source locations from where material (defined by grade blocks) is excavated [3, 31, 36, 37].

• **Blast:** A blast is an area of ground or total broken stocks prepared for digging [37, 38]. Blast material can include coal, ore, spoil, waste, or overburden [37].

• **Bench:** A bench is a mine location [31].

• **Grades and Material:** Blasts contain specific grades (types of material with individual dumping or blending requirements) within them [38, 39]. Accurate grade information for the blast location is necessary for assignments, ensuring the correct material is hauled to the appropriate dump [40, 41].

4. Callpoints (Network Nodes)

**Callpoints** are essential, non-material handling locations that define the structure of the haul road network [3, 31].

• **Road Definition:** Callpoints indicate the **start and end locations of a road segment** [42, 43]. They are typically placed at intersections, ramps, hills, and low-lying areas [44, 45].

• **Elevation:** It is critical to set the **elevation** for Callpoints so the system can accurately calculate the road gradient (slope), which directly affects travel time calculations for the Best Path (BP) module [44-47].

• **Reassignment:** Strategic callpoints can be flagged as **reassignment callpoints**, triggering Joptimiser to re-evaluate a truck's current assignment to ensure it remains optimal given dynamic mine conditions (like delays or slow-downs ahead) [42, 48, 49]. When a truck enters the road segment ending with a reassignment callpoint, the system initiates the assignment check [50, 51].

Locations Utility (LocMod / MineView Location Utility)

The **Location Utility** or **LocMod Utility** is the tool used to manage all these location types, handling tasks such as displaying, finding, creating, editing, and deleting locations [31, 52-56].

• The utility allows direct input of data points like **Easting, Northing, and Elevation** coordinates [57, 58].

• It is used to define key operational constraints, including a location's **Status** (Ready, Down, Delay, Standby, Shiftchange), **Max Trucks**, material acceptance lists, and **Blending Parameters** [1, 16, 52, 58, 59].

• The system recognizes various location types, such as Dump, Crusher, Stockpile, Blast, Call Point, Workshop, Shiftchange (Tiedown), Pit, and Region [3, 58, 60].

--------------------------------------------------------------------------------

## Mine Location Hierarchy and Management Utility

The sources establish a clear hierarchical structure for defining the physical space within the mine model, managed primarily through the **Location Utility** (also referred to as LocMod Utility), progressing from the broadest entities down to specific operational points: **Pits > Regions > Specific Locations**. This hierarchy is foundational for organizing the mine environment, enforcing constraints, and supporting the optimization algorithms.

1. The Hierarchical Structure of Locations

The mine management system stores locations in a strict hierarchical fashion, moving from general definitions to specific ones [1].

• **Pits (Top Level):** The **Pit** is the most general type of location, residing at the top of the hierarchy [1]. A pit is considered a **"true" pit** if it does not share haul trucks with other pits [1, 2]. This isolation is critical because the system determines truck requirements for each pit separately and does not share trucks between pits [3].

• **Regions (Middle Level):** A **Region** is the second most general location type and sits at the middle of the hierarchy [1]. A single pit can contain several regions [1]. Regions are typically unique areas defined by geography or operating conditions, such as the shop area (Region Shops) or specific stripping benches (Region Stripping) [1]. Unlike pits, regions can share trucks [1].

• **Specific Locations (Bottom Level):** The most specific types of locations reside at the bottom of the hierarchy [1]. These encompass all functional points used for daily mining operations, including:

    ◦ **Shops** (workshops, fueling areas) [1, 4].

    ◦ **Dumps, Crushers, and Stockpiles** [1, 4].

    ◦ **Benches, Blasts, and Callpoints** [1, 3, 4].

    ◦ **Tiedown/Parkup areas** (Shiftchange locations) [1, 3-5].

The procedure for adding a new location must respect this structure, proceeding from the **general (Pit) to the specific (location)** [5]. For example, to add a new dump to a pit that contains a region, you must first define the region and then define the dump within that region [5, 6]. Pit and region locations do not require coordinates, but all other specific locations do require Northing, Easting, and Elevation values [6].

2. Management and Utilities (LocMod / Location Utility)

The **Location Utility** (or LocMod Utility) is the dedicated tool for maintaining this hierarchy and its associated data, enabling the addition, deletion, display, and modification of location information [4, 7, 8].

Key location information managed within this utility includes:

• **Coordinates and Elevation:** The utility stores Easting, Northing, and Elevation data (in feet or meters) for locations, which helps define the **spatial relationship** among points in the pit [6]. Accurate elevation (Z coordinate) data is essential, as it determines the road segment gradient, which, in turn, impacts the Best Path (BP) and Linear Programming (LP) calculations used for assignments [9, 10].

• **Status and Control:** The utility allows control over the **Status** of specific locations (Ready, Down, Standby, Delay, Shiftchange) [11, 12]. Setting the status governs whether automated truck assignments are generated for that location [11, 13].

• **Restrictions and Constraints:** The Location Utility is used to define key operational constraints, such as **Dump Capacity (tons/hr)** and **Target Feed (tons/hr)** for disposal points, and which **Materials** are **Accepted at this Location** [12, 14, 15]. These constraints are critical inputs for the LP model [16, 17].

3. Impact on Data Integrity and Optimization

The accurate configuration of the Pit > Region > Location hierarchy is crucial for the entire optimization system (Joptimiser/DISPATCH) to function correctly and maintain **Data Integrity** [18, 19].

• **Haulage Assignment:** The LP layer checks location constraints during its process of creating feasible routes [20, 21]. For a shovel to receive assignments to a dump, that dump must be in a **Ready** status and **accept the material** being dug [22-24]. If a shovel moves to a new location and no region is defined for that location, or if the pit is undefined for that region, assignment pathways will not be created, preventing the shovel from appearing in the LP solution [25-27].

• **Road Network Integrity:** The hierarchy provides the nodes for building the **Road Network** [28, 29]. The Best Path (BP) model relies on this spatial representation to calculate the shortest travel routes, emphasizing the importance of accurate location and elevation data within the hierarchy [19, 30, 31].

• **Region Locking:** Regions enable restrictions, allowing components (trucks, shovels, processors) to be grouped together. Trucks can be **locked to a Region**, preventing assignments outside that defined area [32, 33]. This helps divide the mine by area for simplified management [34].

--------------------------------------------------------------------------------

## Mine Location Statuses and Operational Impact

The operational **Status** of a location (such as **Ready, Down, Delay, or Shiftchange**) is a critical piece of configuration data managed primarily through the **Location Utility** (also known as LocMod Utility or Dispatch Utility). This status directly influences the core functions of the mine management system, especially the optimization and assignment algorithms, and is paramount for maintaining **Data Integrity** and efficient operations.

Key Location Statuses and Their Meanings

The sources recognize several primary operational statuses that apply to various mine locations, including dumps, crushers, stockpiles, and shops [1-4].

|   |   |   |
|---|---|---|
|Status|Definition|Operational Impact|
|**Ready** (Mining Activity)|The location is functional, available for use, and working in mine production [1-3, 5, 6].|The system considers Ready locations in its optimization and assignment calculations (e.g., sending Ready trucks to Ready shovels and dumps) [1, 3, 7].|
|**Down**|The location does not function properly and is not in use (part of Down Time/Planned or Breakdown Loss) [1-3, 5, 6].|The assignment system **does not consider Down locations** in its optimization or assignment calculations [1, 3, 8]. If a crusher goes Down, its capacity is greatly reduced, causing the system to reduce truck assignments to feeding shovels, starting with the lowest priority ones [9, 10].|
|**Delay** (Operating Delay)|The location is temporarily inactive but is still considered available for optimization planning (part of Operating Delay Time) [2, 3, 5, 6, 9].|The system **considers Delay locations** in its optimization and assignment calculations, but generally **does not assign trucks to a shovel with a Delay status** [3, 9, 11]. Trucks assigned to a Delayed location are expected to arrive, although not necessarily at the original estimated time [11]. Delays should typically be for short periods (e.g., 30 minutes or less) or when repair time is unknown [11].|
|**Standby** (Operating Standby)|The equipment/location is available for work but is not actively used (e.g., due to lack of work or no operator) [1-3, 5, 6].|The system **does not consider Standby equipment/locations** in its optimization or assignment calculations [1, 3, 8].|
|**Shiftchange** (Tiedown/Parkup)|Equipment status automatically changes to Shiftchange when operators log off at shift end in mines with production breaks [2, 12]. This status is synonymous with Tiedown [13, 14].|The system **considers Shiftchange locations/equipment** in optimization and assignment calculations in anticipation of the next shift's operators logging on [13].|

Status Management and Data Integrity

The statuses of locations are managed through the **Location Utility** (or LocMod Utility/Dispatch Utility) [15-19]. Dispatchers routinely check the statuses of required locations (dumps, crushers, shops, etc.) before a shift to ensure they are **Ready, Delay, or Shiftchange**, and conversely, place non-required locations on **Down** or **Standby** [2, 20].

**Data Integrity** is profoundly affected by correct status management:

1. **Enforcing Assignments:** The status determines if a location is included in the feasibility calculations of the Linear Programming (LP) layer [21, 22]. Trucks will not be automatically sent to destinations that are **Down** [21]. If a dump is **Down** or **Standby**, the LP solution will not create feasible routes involving that dump [23].

2. **Handling Exceptions:** Changes in status (to Down, Delay, or Standby) generate exceptions that the Mine Controller must address immediately to prevent disruption to the haulage cycle [24-27]. For instance, if a shovel or crusher changes status, the assignment system may need to recalculate the LP solution [28, 29].

3. **Impact of Incorrect Status:** If a location intended for use is set to **Down** or **Standby**, the optimization system will ignore it, leading to inefficient assignments, queuing, or idle equipment elsewhere in the pit [20, 30]. If a shovel is expected to be Down for a long time, the dispatcher should manually change its status from **Delay** to **Down** so the system stops considering it in assignment calculations [31].

4. **Shop and Tiedown Statuses:** For service locations (shops), a status of **Standby** or **Down** makes the location ineligible for automatic truck assignments, although **Ready** or **Delay** shops are eligible [32]. For **Tiedown Locations**, the system considers equipment on the **Shiftchange** status in its optimization model to facilitate a smooth subsequent shift [13].

In sum, location statuses are central controls configured via the Location Utility that ensure the automated assignment engine adheres to the real-time operational availability and tactical goals of the mine plan [2, 33].

--------------------------------------------------------------------------------

## Mine Optimization: Capacity and Target Feed Rates

The sources extensively discuss **Target Feed Rate (Tons/Hr)** and **Dump Capacity (Tons/Hr)** as crucial parameters configured within the mine management system's **Location Utility** (also referred to as LocMod Utility, Dump Utility, or Dispatch Utility) that directly influence the Linear Programming (LP) and Dynamic Programming (DP) models responsible for optimizing truck assignments and material flow.

These values define the operational limits and production constraints of dumping locations (sinks), primarily crushers, dumps, and stockpiles.

Definition and Differentiation of Target Feed Rate and Dump Capacity

In systems like DISPATCH, these two measurements manage throughput at destination locations:

1. **Dump Capacity (Capacity Tons/Hr):**

• This field specifies the **maximum tons/hr allowed to dump at the location** (or maximum processing capacity) [1, 2].

• For **crushers**, this value should be set to the **engineering maximum rate** of the crusher machine [3, 4].

• For standard dumps and stockpiles, this value is generally set to **"No Limit"** (or 5000 tons per hour) because these locations usually take whatever amount the trucks bring until they become full [1, 5].

• The Dump Capacity is a **"softer" restriction** that uses an upper value limit that must not be exceeded [6].

2. **Target Feed Rate (Target Feed, tons/Hr):**

• This field specifies the **current or "target" processing capacity** expected at the dumping point [7-10].

• This is typically the expected average crusher production rate per hour over the shift [8].

• For crushers, the Target Feed Rate can be adjusted to influence truck flow, even if the absolute capacity is higher (e.g., set to 2500 tph if the maximum is "No Limit") [5, 9].

• The Target Feed Rate is characterized as a **"harder" constraint (a hard restriction)** that uses an absolute figure and is broken down per hour across the shift [11]. If a target is not met in one hour, the deficit is accumulated and added to the target for the next hour to meet the full shift target [6].

• For dumps and stockpiles, this is usually set to **"No Target"** unless special circumstances require restricting material flow (e.g., restricting flow to allow a bulldozer to keep up with incoming material) [5].

Role in the LP Solution and Optimization

The values configured for Target Feed Rate and Dump Capacity are fundamental constraints used by the Linear Programming (LP) layer of the optimization system (Joptimizer/DISPATCH) to determine optimal **haulage production circuits and feed rates** [12-14].

• **Constraint Enforcement:** LP incorporates Dump Capacity and Target Feed Rate constraints to ensure that material flow into a destination meets specified production goals [15, 16].

• **Crusher Throughput:** The **Nominal Rate** (which corresponds to the Dump Capacity for crushers) is critical for determining the maximum processing rate. For crushers, the Nominal Rate should be set to the **engineering maximum capacity** [3, 4]. The calculated throughput at a processor is the nominal rate adjusted for processor efficiency [3].

• **Influence on Assignments:** The assignment engine uses these capacity values to allocate trucks. If the system detects that the required tonnage (feed rate) into a destination exceeds the set capacity, the LP solution will adjust flow rates or divert trucks:

    ◦ When the dump capacity is reached, trucks will be **diverted to other dumping locations** [17].

    ◦ The system can prevent **queuing** by limiting the rate or capacity. For example, if a loading tool's output far exceeds the crusher's capacity (assuming no stockpile overflow), Joptimizer may suggest placing the lowest priority shovel on Standby or "unuse" the shovel to lessen the flow of material [18-20].

• **Proportional Sharing (Global LP Parameter):** If multiple shovels are feeding a constrained crusher (i.e., total shovel loading rates exceed crusher capacity), the global parameter "Proportionally Share Materials Dumping Capacities among Shovels" can be enabled. If set to YES, LP calculates a ratio of dumping capacity to total loading capacity and proportionally **lowers the feed rates** of the constrained shovels to ensure the limited material processing capacity is shared, preventing any shovel from going unused due to limited crusher capacity [21-24].

Context in the Location Utility (LocMod / Processor Assistant)

These settings are managed by the Mine Controller, primarily through the Location Utility or Processor Assistant interfaces.

• **Location Utility/LocMod:** The utility explicitly displays fields for **Target Feed, tons/Hr** and **Dump Capacity, tons/Hr** [1, 25, 26].

• **Processor Assistant:** This utility is used to review and adjust processor settings. It features an **Efficiency tab** where the **Nominal Rate** (Crusher Capacity) and **Effective Rate** are displayed/configured. The Effective Rate is the product of the Nominal Rate and the Efficiency percentage [4].

• **Monitoring:** The LP Summary Report displays the value of **Nom Dig/Dmp** (Nominal Dig/Dump rate) which reflects the shovel's loading rate for dump-to-shovel paths, and the dumping point's capacity (Target Feed Rate or Capacity Limit) for shovel-to-dump paths [27, 28]. This allows monitoring of whether the actual operation aligns with the defined capacities and targets.

If dump targets are set but not achieved on time, the system accumulates the deficit, potentially causing the next assignment scenario to be skewed towards meeting that accumulated target [6, 29]. Therefore, prudent use of these parameters is crucial for optimal truck assignment and maintaining data integrity [30].

--------------------------------------------------------------------------------

## Mining Traffic Control: Max Trucks at Location

The concept of **"Max Trucks at this Loc"** (Maximum Trucks at this Location) is a crucial operational parameter within the mine management system, specifically configured within the **Location Utility** (also referred to as LocMod Utility) to manage traffic flow and prevent queuing at processing or service destinations.

This constraint operates within the broader context of managing **Locations** and is vital for the optimization functions, particularly controlling throughput at dumping points and maintenance areas.

Definition and Scope of Max Trucks at Location

The `Max Trucks at this Loc` field specifies **the maximum number of trucks allowed at the location at the same time** [1, 2].

This parameter is specifically relevant to locations defined as **shops** (including workshops, fueling areas, tire shops, etc.) or locations designated as **Tiedown** areas [1-3]. For crushers and dumps, the concept of maximum traffic limit is generally handled by other parameters, although the `Max trucks` field is mentioned in the context of `Crusher Rates/Limits` sections [4, 5].

Context in Location Utility and LocMod Utility

The Location Utility (or LocMod Utility) is the interface used by the dispatcher or builder to display, create, and modify location data, including the constraints that affect traffic control [6-10].

Within the Location Utility interface:

• **Tiedown and Shop Locations:** The `Max Trucks` field explicitly appears in the **Crusher Rates/Limits** section of the Details tab in the Properties pane of the Location Utility [4]. It is specifically noted that this field **only appears if the location is a Tiedown or Shop location** [2].

• **Fueling Stations:** The system uses a specific capacity setting for fuel bays to control truck flow. When a truck's fuel level drops below the "fuel low percent," the optimizer considers sending the truck to the fuel station, but only if the **current queue has not exceeded the limit** set by the fuel bay's **Queue Capacity** [11-13]. This queue capacity setting functions similarly to `Max Trucks at this Loc` by limiting access to a specific service location [12].

For standard dumps and stockpiles, the "maximum processing capacity" is defined by the **Dump Capacity** (in tons/hr), which is often set to "No Limit" (or 5000 tons per hour) because these locations usually accept whatever amount trucks bring until they become full [1, 14, 15]. However, for crushers, the maximum capacity is more pertinent to actual tons per hour processing limits [15, 16].

Impact on Optimization and Assignments

The `Max Trucks at this Loc` value serves as a constraint used by the optimization algorithms to prevent resource bottlenecks and excessive queuing:

1. **Queue Management:** This setting directly controls the maximum number of trucks allowed to queue or back into position at a specified destination [17]. When the number of trucks currently queuing at a dump location reaches the Queue Capacity (which is functionally the maximum limit for trucks at that location), the assignment engine **will divert trucks to other acceptable dumping locations** [17]. This logic is crucial for managing crusher queues, preventing trucks from queuing unnecessarily, and redirecting them to available stockpiles or dumps [17, 18].

2. **Fuel and Service Assignments:** The constraint is essential for managing automated service assignments. For instance, a truck eligible for refueling is assigned to a fuel shop only if the number of trucks at or en route to the shop is **fewer than the allowable number** [19, 20]. Similarly, the fuel bay may have a queue capacity set to limit the number of trucks assigned there [12]. When a truck's fuel level reaches the "Fuel Empty Percent," the system ignores the queue capacity restriction and sends the truck immediately to the nearest ready fuel bay [12, 13, 21].

3. **Tiedown/Parkup:** The `Max Trucks` constraint also ensures that parking logistics, especially during shift change (**Tiedown**), are respected, preventing more equipment from gathering at a designated tiedown area than it can physically accommodate (e.g., controlling the maximum number of operators pre-set for a tiedown location to ensure ground transport and building limits are not exceeded) [2, 22].

--------------------------------------------------------------------------------

## Managing Equipment Tiedown Locations and Procedures

The sources provide extensive information on **Tiedown Location Status** (often referred to interchangeably as Shiftchange or Parkup locations) within the **Locations** management framework (using utilities like the **Location Utility**, **LocMod Utility**, and **Tiedown Utility**) [1-7].

These locations are vital for managing equipment at the end of a shift and ensuring a smooth transition for the next shift, directly impacting the planning and optimization processes [6, 8, 9].

Definition and Purpose of Tiedown Locations

A **Tiedown Location** is a designated area in the mine where equipment, particularly haul trucks, parks at the end of a shift [6, 10]. These areas are also known as **Shiftchange** or **Parkup** areas [5, 11].

The primary functions associated with setting up and managing a Tiedown location are:

1. **Shift Change Management:** Tiedown locations allow for an operator change to occur or facilitate the process of placing trucks on **Standby** if they are not scheduled for the upcoming shift [6, 8, 11-13].

2. **Assignment Control:** When a mine uses specific shift change strategies, trucks are automatically assigned to these locations to avoid unnecessary idle time or costly overtime from a late tiedown [8, 14].

3. **Optimization:** Equipment on **Shiftchange** status is still factored into the optimization and assignment calculations by DISPATCH in anticipation of workers logging on to the equipment shortly after the shift starts [9, 15].

Configuration and Status Management (Location Utility / LocMod)

The definition and management of a location as a tiedown area are typically handled within the **Location Utility** (or **LocMod Utility**) [2, 7, 16-19].

Designating a Tiedown Location

A location is designated as a tiedown area using a specific flag or checkbox within the utility interface:

• The **Location Utility** or **LocMod Utility** features a field labeled **Tiedown Location?** (or simply **Tiedown**) [2, 10, 13, 20].

• To make a shop or location a valid tiedown area, the dispatcher selects the **Tiedown?** field, changing its status from **NO** to **YES** (or marking the checkbox) [4, 13, 21, 22].

Tiedown Location Status

Like other locations (dumps, crushers, shovels), a Tiedown location also has an operational **Status** (e.g., Down, Ready, Standby, Delay, ShiftChange) [23-25].

• If the location status is set to **Down**, **Delay**, or **Standby**, DISPATCH **does not send trucks** to that location for assignments [23, 26].

• A shop location designated as a **Tiedown** location typically appears with a **Ready** status if it is available for use [27].

• The **Shiftchange** status is synonymous with the **Tiedown** status [15, 28]. When operators log off equipment in mines with a shift change break, the equipment status automatically changes from **Ready** to **Shiftchange** [12, 28]. The system considers equipment with the **Shiftchange** status in optimization calculations, anticipating the next shift's logons [9, 15].

Automated Tiedown Procedures (Tiedown Utility)

The **Tiedown Utility** (form name `parkup`) is dedicated to managing end-of-shift truck assignments to these tiedown locations [3, 5, 6, 29]. It can operate in **Automatic Mode** or **Manual Mode** [6, 30].

Automatic Mode

In Automatic Mode, the system optimizes each truck’s last load assignment and determines where the trucks should park based on **user-defined tiedown procedures** and **variables** [6]. Procedures define the acceptable areas for tiedown assignments (e.g., Tiedown at Locations, Tiedown at Excav/Loaders, or Tiedown at Shovel/Location) [31-33].

Key parameters for defining a tiedown procedure include:

• **Start Time:** The time the tiedown procedure begins, triggering a new LP calculation and locking trucks to shovels for review [34].

• **End Time:** The time when all trucks should be at their tiedown locations for end-of-shift operations, after which DISPATCH stops generating tiedown assignments [35].

• **Length:** The duration during which assignments can be generated [36].

• **TIEDOWN Variable (Configuration):** This variable, set in `config.prv`, determines if the system can send a loaded truck to a tiedown location (**LOADED**) or if the truck must first dump its load (**EMPTY**) before receiving a tiedown assignment [37, 38].

Assignment Logic during Tiedown

The system uses specific decision logic to determine assignments during the tiedown period [39]:

• **Priority of Assignments:** If a truck has a **pre-assigned tiedown location**, the system assigns the truck to that location, even if the truck will arrive late (i.e., after the **End Time**) [40].

• **Proximity and Feasibility:** If no pre-assigned location exists, the system checks various scenarios based on the defined tiedown procedure. For instance, in the "Tiedown at Locations" procedure, if the shovel is not a valid tiedown location, the system assigns the truck to the **closest tiedown location** it can reach before the End Time [32, 41]. If no location can be reached by the End Time, it assigns the truck to the tiedown location **closest to the truck’s current position**, accepting late arrival [41, 42].

• **Shovel Locks:** During the start of the shift-change window, the system locks each truck to a shovel near its current location to accommodate operator exchange [43]. Shovels can also be explicitly designated as a **tiedown location** [44-46].

Operational Monitoring

Tiedown locations and associated assignments are monitored primarily through the **Haul Route Perspective** (JSMTC), where trucks reporting to tiedown locations appear below the appropriate location in the **Service Locations** section of the **Queues** display [11, 47]. The **Truck Summary Report** also includes the **Shiftchange Location** for each truck [48].

--------------------------------------------------------------------------------

## Mine Haul Roads and Optimization Data Integrity

The sources define **Haul Roads** as a fundamental element of the mine model within systems like DISPATCH and Jmineops, and the **Road Utility** (or **RoadMod Utility**) as the primary tool for managing this component. The accurate management of haul roads is critically tied to maintaining **Data Integrity** because the road network directly underpins the optimization algorithms used for truck assignments and cycle time calculations.

Definition and Purpose of Haul Roads

A haul road is explicitly defined as **the section of road connecting two consecutive locations** in the mine, such as between two callpoints, a shovel and a dump, or a blast and a dump [1, 2].

• **Structure:** Haul roads permit two-way traffic, requiring two corresponding **road segments** in the system: one for travel from location A to B, and one for travel from location B to A [1, 3, 4]. A collection of these individual haul roads forms a **haul route**, which connects two endpoint locations [3, 5].

• **Function in Optimization:** The road network serves as the **scale representation of the pit** that the Best Path (BP) optimization model uses to calculate the shortest travel routes between any two points [6-10]. The road data is essential for calculating empty and loaded travel times (in seconds) [11, 12].

The Role of the Road Utility

The **Road Utility** provides the necessary functions for dispatchers and builders to manage the road network, ensuring the virtual model matches the physical environment [13-16]:

1. **Creation and Deletion:** The utility allows users to **add and delete haul roads** when pit conditions change [2, 13, 17, 18]. When one road segment is defined (e.g., Start to End), the system automatically defines the return segment [19].

2. **Traffic Control:** The utility is used to **open and close haul roads** to manage traffic flow [13, 14, 20]. This is achieved by manipulating the status of the road segments:

• **Open Forward/Open Return:** These statuses define permitted traffic direction, allowing the configuration of **one-way roads** by clearing the checkbox corresponding to the forbidden direction [21-23].

• **Closed:** When a road segment is set to closed, the system forces the assignment engine to choose an alternative path [24, 25].

3. **Data Input:** The utility fields display and allow modification of critical inputs such as the **Start and End locations**, **distance**, and **grade percentage** [26]. This information is derived from the coordinates and elevations of the connected locations [27-30].

Critical Link to Mine Information and Data Integrity

The maintenance of the haul road network is fundamental to the system's overall **Data Integrity**, as inaccurate road data directly undermines the optimization engine, leading to inefficient and costly operations [8, 10, 31-37].

• **Assignment Failures:** **Without accurate knowledge of the mine [road network], Fleet is unable to make the best decisions** [31-34]. This can result in trucks receiving poor assignments, causing queuing, idle loaders, and misdirected loads, which leads to higher costs per ton [8, 31-34].

• **Optimization Reliance:** The Linear Programming (LP) and Dynamic Programming (DP) models rely heavily on the road network to calculate feasible production circuits and required cycle times [38-42]. If a road is closed or its data is faulty, the assignment engine's path selection is affected [5, 43, 44].

• **Impact of Inaccurate Geometry:** It is vital to set **elevations for locations and callpoints** correctly so the system can accurately calculate the **gradient** (slope) of road segments [29, 30, 45]. Incorrect elevations or speed limits can cause the system to avoid using specific roads, potentially leading to assignment exceptions that appear in the **Exception View** [30, 44, 46, 47].

• **Misroutes and Tracking:** If operators fail to follow the assigned path ("green line"), a **misroute exception** is generated, indicating that the assignment is invalid and triggering a reassignment request [45, 48-52]. Inaccurate road geometry can lead to **incorrect misroute errors** [53]. Dispatchers can run GPS Traces to visually verify that actual truck paths align with the modeled road segments [54-56].

• **Operational Constraints:** Constantly closing haul routes is explicitly discouraged because it **adversely affects the system’s ability to generate optimum truck assignments** [43].

The sources stress that the dispatcher must continuously monitor and maintain a **"healthy road network"** to ensure optimization remains effective [10]. The process of defining road attributes, such as road category, and ensuring correct elevations is essential, as this data is used for calculating accurate travel times, which subsequently drives the assignment decisions and ETA predictions [30, 57, 58].

--------------------------------------------------------------------------------

## Mine Haul Road Structure and Management

The phrase **"Between two consecutive locations"** is the fundamental definition used by the sources to describe a **Haul Road** or **Road Segment** in a mine's network, which is managed through utilities like the **Road Utility** or **RoadMod Utility**.

Definition and Structure of Haul Roads

A haul road is explicitly defined as **the section of road connecting two consecutive locations** in the mine [1, 2]. These locations are nodes in the mine network, which can include various mine entities such as callpoints, blasts, dumps, stockpiles, crushers, shops, and benches [1-4].

Key aspects related to the structure of roads:

• **Road Segments:** Because haul roads naturally permit two-way traffic, the DISPATCH system requires **two road segments** for each physical road: one segment for travel from the **Start Location to the End Location** (Forward direction), and another segment for travel from the **End Location to the Start Location** (Return direction) [3, 5, 6].

• **Haul Routes:** A collection of these individual haul roads forms a **haul route**, which is defined as the section of road that connects two endpoint locations, such as a shovel and a waste dump [3, 7].

Management via Road Utility

The **Road Utility** (or **RoadMod Utility**) provides the necessary interface to manage these road segments and their traffic flow configurations:

1. **Status Configuration:** The status of each segment (Open Forward, Open Return, or Closed) is critical for traffic control.

• **One-Way Traffic:** To create a one-way road, the dispatcher explicitly closes one of the segments, permitting traffic only in the direction of the remaining **Open** segment [6-12].

• **Closed Roads:** If both segments are closed, the road is completely closed, forcing the assignment engine to select another path when assigning a truck [7, 11, 13].

• **Visual Indicators:** Closed haul roads may be colored red on the map, and one-way roads are often brown with an arrow showing the permitted direction [11, 14].

2. **Creation and Deletion:** The utility allows dispatchers and maintenance personnel to **add and delete haul roads** when new roads are added to, or removed from, the haulage road network [2, 15-18]. When adding a road, defining one segment automatically creates the other segment [19].

3. **Data Integrity:** Maintaining accurate location data is essential for the road network to function effectively [20, 21]. The utility handles key data derived from the locations:

• **Elevation and Gradient:** It is crucial to set elevations for locations and callpoints to allow the optimization system (BP) to accurately factor in the **gradient** (slope) of the road segments between consecutive locations [22-24]. This gradient information is used to calculate travel times [23, 25-27].

• **Travel Times:** The utility displays the automatically calculated Empty and Full travel times (in seconds) between the two locations, which are based on distance and grade data, and informed by real-time travel information collected by the system [25, 26, 28, 29].

Importance in Optimization

The correct definition of the haul road network, sectioned by "consecutive locations," is foundational to the mine's optimization efforts:

• **Best Path (BP) Calculation:** The BP model uses this road network (and its associated metrics like elevation and status) as the scale representation of the pit to calculate the **shortest distance** and **best available path** between two points [20, 27, 30, 31].

• **LP Feasibility:** The Linear Programming (LP) model relies on the status and physical characteristics of these segments to determine **feasible routes** for production circuits [32-35]. For instance, if a road segment between two locations is closed, the LP solution will not use any production circuits involving that path [35].

• **Continuous Monitoring:** Since roads and ramps are constantly changing in a dynamic mining environment, dispatchers must regularly monitor and update the road network to ensure its accuracy [20, 36]. Inaccurate road segments can lead to **misroute errors** and incorrect truck assignments [37, 38].

--------------------------------------------------------------------------------

## Haul Road Statuses and Traffic Control

The statuses **Open Forward, Open Return, and Closed** refer to the configuration options available for **Haul Roads** or **Road Segments** within mine management systems like DISPATCH and Jmineops, specifically managed via the **Road Utility** or Haul Route Utility. These statuses are crucial for controlling traffic patterns and ensuring the assignment engine calculates valid and efficient routes for haul trucks.

Road Characteristics and Segments

A haul road is defined as the section of road connecting two consecutive locations in the mine, such as between two callpoints or a blast and a dump [1, 2]. Because haul roads inherently permit two-way traffic, the system typically requires **two road segments** for each road: one segment for travel from location A to location B, and another segment for travel from location B to location A [3, 4].

The statuses applied to these segments dictate the allowed flow of traffic:

• **Open Forward/Open Return (One-Way Traffic):**

    ◦ The system uses the **Open Forward** checkbox to indicate that traffic is permitted in the direction from the **Start location to the End location** [5].

    ◦ The **Open Return** checkbox indicates that traffic is permitted in the direction from the **End location to the Start location** [6].

    ◦ To make a road a **one-way road**, the dispatcher removes the check from the checkbox corresponding to the forbidden direction (e.g., clearing the **Open Forward** box closes the road in that direction) [5, 7-9].

    ◦ On the JSMine Map (MineGraphics), one-way roads are often colored **Brown** with an arrow indicating the permitted traffic direction [10, 11].

• **Closed (Road Blocked in Both Directions):**

    ◦ A road is considered **Closed** when neither the **Open Forward** nor the **Open Return** checkboxes are marked, preventing traffic in both directions [11, 12].

    ◦ Closing a road segment forces the assignment engine (DISPATCH/Jmineops) to select another path when assigning a truck along that route to an endpoint [13, 14].

    ◦ A completely closed road may be colored **Red** on the map [10, 11].

Management and Context within the Road Utility

The **Road Utility** (or **RoadMod Utility**) provides the interface tools necessary for dispatchers to manage these statuses and maintain the accuracy of the road network [15, 16].

• **Road Utility Interface:** The utility displays key data fields such as the Start and End locations, travel distance, grade percentage, and the statuses of the **Open Forward** and **Open Return** segments [5, 17].

• **Controlling Traffic:** Dispatchers use this utility to **open and close haul roads** and routes during the shift to control traffic patterns in the mine due to changing pit conditions [15, 18-20].

• **Creating One-Ways:** To establish a one-way flow, the dispatcher modifies the road in the utility by closing one of the road segments, permitting truck traffic only in the direction of the remaining open road segment [7, 21, 22].

• **Operational Impact:** Closing a haul road affects the path selection by the optimization system, forcing the Best Path (BP) algorithm to select alternative routes [13, 14]. However, closing a haul route (multiple roads connecting two endpoints) is more restrictive, preventing the assignment engine from sending trucks between those two endpoint locations entirely [13]. The sources warn that constantly closing haul routes adversely affects the system’s ability to generate optimum assignments [23].

Consequences for the Assignment Engine

The status of the road network directly affects the efficacy of the optimization models (LP and DP):

• **Route Feasibility:** The LP algorithm considers the road status (open or closed) along with restrictions, elevations, and capacities to determine if a **feasible route** exists for production circuits [24, 25]. If a road segment is closed, the route containing that segment will not be considered a feasible path for the LP solution [26].

• **Troubleshooting:** If the system is **not using a specific road or path** for assignments, the dispatcher may check the **Exception View** to determine if the road is too steep, has a speed limit, or is flagged as a route closure [27-29].

• **Misroutes:** If operators drive trucks off the assigned "green line," or the established road segment, a misroute exception is generated because the system relies on trucks traveling along the designated road network. This reliance requires the electronic road network model to be continuously monitored and updated to reflect reality, which includes correctly setting road statuses [30, 31].

--------------------------------------------------------------------------------

## Mine Grade and Blending Utility Management

The sources emphasize that **Grade & Blending**, managed through tools such as the **Grade Utility** and the **BlendMod Utility**, are vital components of the mine information system, fundamentally linked to ensuring **Data Integrity** and optimizing production outcomes. These functionalities translate geological and quality data into operational constraints that drive the truck assignment algorithms.

1. The Foundation: Defining Grades (Grade Utility)

The **Grade Utility** is responsible for creating and maintaining records that define the qualitative properties of the material being mined [1-3]. This information forms the essential input for all subsequent material management and blending activities [4-6].

• **Definition of Grades:** A grade describes the material's properties, such as acidity, color, specific gravity, or sulfur content, and multiple grades often combine to define a complete material type [3, 4, 7]. Grades are automatically collected when material is excavated, but the **Builder** or Ore Control Department may need to manually create or update grade records [2, 5].

• **Types of Grades:**

    ◦ **Continuous Grades** represent numerical scale values, such as percentage () or grams per tonne (textg/t), like Fe or Au content, which have minimum and maximum boundaries [8, 9]. They are subject to **arithmetic averaging** during continuous blending calculations [10].

    ◦ **Discrete Grades** represent categorical values or simple tags attached to mining blocks (e.g., Royalty, Source) [8]. These are crucial for blending by category [11].

• **Grade Creation:** Creating a grade involves specifying its **Type** (Continuous or Discrete), providing a **Name**, and selecting the **Weight Averaging Method** (typically Mass) [9]. For continuous grades, units, and maximum, desired, and minimum values must be defined [9]. For discrete grades, specific discrete values are required [8, 9].

2. The Control Mechanism: Managing Blending (BlendMod Utility)

The **BlendMod Utility** uses the grade data defined in the Grade Utility to enforce blending parameters at dumping points like crushers and stockpiles [12, 13]. The goal is to ensure the material flow meets specific composition and quality requirements [14].

• **Blending Options:** The BlendMod utility typically provides three main options for controlling blending [13, 15, 16]:

    ◦ **Continuous Blending by Component:** Regulates the percentage of specific material qualities (continuous grades) within the blend over a defined **Control Mass** (tonnage) or **Control Interval/Duration** (time) [16-18]. When setting this up, the **Current** value for each component should be placed halfway between the minimum and maximum limits to reflect the assumption that the desired blend is already being produced [19, 20].

    ◦ **Continuous Blending by Category:** Manages ratios between different discrete grades (categories, e.g., High Grade, Low Grade) delivered in desired proportions over a defined control quantity [11, 21].

    ◦ **Batch Blending by Component:** Requires the blend to meet specifications only when the total **production target is achieved**, offering short-term flexibility but carrying higher risk if production is interrupted [22-24].

• **Control Parameters:** The blend definition includes a **Control Mass** or **Control Duration**; the mass should ideally be at least five times the size of the largest truck to effectively satisfy blending requirements [25-27].

3. Impact on Mine Information Components and Data Integrity

The accurate setup and maintenance of Grades and Blending constraints are paramount because they integrate directly with the automated planning and assignment algorithms (Linear Programming/LP) and directly affect the overall **Data Integrity** of the operation [4, 28-30].

• **LP Integration and Optimization:** Blending constraints are fundamental inputs for the LP model, which calculates optimal haulage production circuits and feed rates [31-33]. LP allocates haulage resources to achieve the required average quality within the specified quality limits [34].

• **Assignment Constraints and Hierarchy:** Grade information dictates material acceptance at destinations [35, 36]. Locks related to grades and shovels create a hierarchy of control for dump assignments [37, 38]:

    ◦ A **Dump Lock on Grade with Hard Lock enabled** will override all other locks and force the truck carrying that specific grade to tip only at the designated dump [39, 40].

    ◦ If no dump lock is explicitly set, the truck will be assigned to the **nearest READY dump** that accepts the material grade [41].

• **Risk to Data Integrity:** If the system is misconfigured (e.g., if a shovel digs material not accepted by its assigned dump), LP configures paths only to dumps that accept the material [35, 42]. However, if grade information is ignored or incorrect (e.g., if material type and grade do not match), it leads to ineffective assignments and potentially corrupts the data model [8, 43-45]. Incorrect or insufficient inputs can result in trucks being sent poor assignments, causing queuing, idle loaders, and increased costs per ton [46].

• **Monitoring and Troubleshooting:** Blending constraints are displayed and monitored, such as in the **Processor Assistant (Blend tab)** [47, 48]. If LP detects issues related to blending (e.g., a grade being ignored or a split material type conflict), specific diagnostic messages are triggered in the **LP Troubleshooting Analyzer** [44, 49-51].

--------------------------------------------------------------------------------

## Mining Material Management: Grades and Blending Utilities

The combination of the **Grade Utility** and **BlendMod Utility** provides the foundational framework within mine management systems (like DISPATCH and Jmineops) for managing the material quality and composition, which is critical for **Data Integrity** and optimizing operations. These components ensure that the material mined and processed aligns with specific production and quality requirements.

1. The Grade Utility: Defining Material Quality

The Grade Utility is the primary tool for establishing the inherent characteristics of the material excavated, which are crucial inputs for the blending process [1].

Defining Grades

A **grade** describes the specific properties of the material to be mined, and multiple grades often combine to define a material type fully, covering aspects such as acidity, sulfur content, and specific gravity [2-4]. The office software automatically collects grade data when the material is extracted by a loading tool [2].

Types of Grades

Grades are categorized based on whether they represent continuous measurements or discrete groupings [4]:

• **Continuous Grades:** These are values placed on a numerical scale with minimum and maximum boundaries, expressed in units like percentages () or grams per tonne (textg/t) [5]. Examples include specific elemental content (e.g., Fe percentage or Au grams per tonne) [5]. When configured in the **Grade Editor**, continuous grades require defining the numerical scale, unit type, and weight averaging method (typically **Mass**) [6, 7].

• **Discrete Grades:** These have non-numerical, categorical values, used for defining categories, royalties, or simple tags attached to mining blocks (e.g., Royalty = Fred, or Source = Stockpile) [5]. Discrete blending, which manages these categories, ensures that the ratios between different discrete grades (including material types) are delivered in the desired proportions over a defined control quantity [8].

The Grade Utility is the interface for creating and updating these records, typically handled by the **Builder** or Ore Control Department, often involving the manual input of type, name, unit type, and setting Maximum, Desired, and Minimum values [1, 6, 9].

2. The BlendMod Utility: Controlling Material Composition

The **BlendMod Utility** is the tool used to enforce the desired quality and composition of material delivered to a sink (such as crushers and stockpiles) [10-12]. This utility configures the rules for **blending**, which ensures that material composition remains within appropriate quality limits [12].

Types of Blending Controlled by BlendMod

The utility supports various blending methodologies, broadly categorized by how the quality control is measured and enforced:

• **Continuous Blending by Component (Quality):** This method ensures that the material flow remains continuously within specified limits based on **material components or qualities** (continuous grades) over a defined **Control Mass** (tonnage) or **Control Interval/Duration** (time) [13-16]. Arithmetic averaging of grade values is explicitly used to calculate the actual grade qualities for continuous blending [17]. For this method to function, a **Current** value must be entered (typically halfway between the minimum and maximum limits) so the system assumes the desired blend is already being produced [18, 19].

• **Continuous Blending by Category:** This focuses on achieving desired proportional ratios between **mining block categories** (discrete grades like High Grade, Medium Grade, etc.) over a designated control quantity [8, 20]. The user inputs a **Target Percent** and an allowed **Percent Deviation** [21, 22].

• **Batch Blending by Component:** This "cake-recipe" approach only requires the material blend to meet its specified qualities **when the total target production tonnage is completed** [23, 24]. This method offers greater flexibility in short-term excavator use but is deemed high-risk because an equipment breakdown could prevent necessary materials from being mined later, causing specification failure [23, 25].

The selection of the **Control Mass** or **Control Interval** is vital; the minimum mass should be at least five times the size of the largest truck to effectively satisfy both blending and production requirements [21, 26, 27].

3. Impact on Mine Information Components and Data Integrity

The data defined in the Grade and BlendMod Utilities forms essential parameters used by the optimization algorithms, making their accuracy foundational to **Data Integrity** and efficient operation:

• **LP Integration and Constraint Enforcement:** Blending constraints directly influence the **Linear Programming (LP) layer** of the Joptimizer/DISPATCH assignment engine [28, 29]. LP creates production circuits and calculates haulage flow rates specifically to achieve the required average quality within the set minimum and maximum limits [28, 30, 31]. If a shovel digs material that the assigned dump does not accept, the LP solution configures paths only to dumps that accept the material, ensuring the material constraints are met [32, 33].

• **Assignment Hierarchy:** Grade information is integrated into assignment control through restrictions and locks. **Grade Dump Locks** (especially those enabled with a **Hard Lock**) represent a high level of assignment control, forcing trucks carrying a specific grade block to tip at a single designated dump, overriding other truck or shovel locks [34, 35].

• **Risk of Poor Data:** Errors in configuring grades or blending constraints lead to optimization failures. If a discrete grade blend is set up incorrectly, the LP solution may ignore the grade information, leading to poor assignments [36, 37]. Continuous blending sometimes risks **poor utilization of excavators** (e.g., underfeeding a high-production shovel) to stay within strict blending limits [38].

• **Monitoring and Validation:** The system monitors blend conformance through the **Processor Assistant (Blend tab)** [39]. Deviations from blending rules generate **Blend Non-Compliance** exceptions, requiring the Mine Controller to intervene [40]. Likewise, shovel operators must correctly enter the **grade ID** after a move or material change to ensure accurate dump assignments [41-43].

In essence, Grade & Blending information is integral to the entire operational model, impacting everything from the theoretical LP solution to the real-time instructions given to truck operators [44-46]. Accurate definition and continuous validation of this data are paramount for achieving reduced dilution, improved recovery, accurate production reporting, and lowering costs per ton [47, 48].

--------------------------------------------------------------------------------

## Mine Material Quality: Grades and Blending Utilities

The sources establish that **Grade & Blending** capabilities, managed through tools like the **Grade Utility** and **BlendMod Utility**, are critical components of the mine information system, directly impacting production optimization, resource allocation, and, fundamentally, **Data Integrity**. These tools define the qualitative aspects of material movement, ensuring that trucks haul the correct type and mix of material to meet processing requirements and production goals.

1. Defining Material Quality (Grade Utility)

The **Grade Utility** (or Grade Editor) is the foundational tool for establishing material characteristics, which are the essential inputs for blending logic:

• **Definition of Grade:** A grade describes the properties of the material to be mined, and multiple grades often combine to define a material type fully (e.g., sulfur content, specific gravity, acidity). This data is often initially received from mine planning packages, though the Builder is responsible for manual creation or updates when new information is available.

• **Types of Grades:** Grades are categorized into:

    ◦ **Continuous Grades:** These use numerical scale values (e.g., in percentages or grams per tonne) and must adhere to predefined minimum and maximum boundaries. Arithmetic averaging of these grade values is used in continuous blending calculations.

    ◦ **Discrete Grades:** These use non-numerical, categorical values, acting as labels or tags attached to mining blocks (e.g., Royalty or Source). Discrete grades are crucial for blending by category.

• **Data Flow:** Correctly defined grade records supply essential information (material characteristics, blend category, material quality values) to the blending process. Accurate grade information is mandatory for proper **dump assignments** and ensuring the right material goes to the correct dumping point.

2. Controlling Material Composition (BlendMod Utility)

The **BlendMod Utility** is the interface used to define and manage blending parameters, controlling material quality at processing sinks like crushers or stockpiles. These blend definitions enforce strict quality control over material movement.

• **Blending Objective:** Blending ensures that the material flowing to a sink is of the desired material composition and stays within appropriate limits, such as minimum and maximum quality parameters.

• **Types of Blending:**

    ◦ **Continuous Blending (by Component/Quality):** This method ensures that the material flow remains continuously within specified limits based on material qualities over a predefined **Control Mass** (tonnage) or **Control Interval** (time). For instance, specifying minimum and maximum percentage values for grade components ensures continuous quality control.

    ◦ **Continuous Blending (by Category):** This method regulates material flow based on the ratios of discrete mining block categories (e.g., High Grade, Medium Grade) over a defined control quantity.

    ◦ **Batch Blending (by Component):** This "cake-recipe" approach only requires the material mix to meet blending specifications when the target production tonnage is completed, offering flexibility but carrying a higher risk of specification failure if an event (like an excavator breakdown) prevents later addition of necessary material.

The choice of **Control Mass** is important: a larger mass allows for greater temporary deviation from limits per truckload, but the mass should typically be at least five times the size of the largest truck to effectively satisfy blending and production requirements.

3. Grade & Blending in the Context of Data Integrity

The accuracy and management of Grades and Blending constraints are paramount for maintaining overall Data Integrity and optimizing operations:

• **Assignment Engine Impact:** Blending constraints defined in BlendMod directly feed into the **Linear Programming (LP) layer** of the optimization engine (Joptimiser/DISPATCH). LP uses these constraints to govern material flow, allocating trucks only to production circuits that adhere to the required quality and composition limits. Trucks will not be automatically assigned to dumping locations that violate current blending rules or material restrictions.

• **Risks of Inaccuracy:** If grade information is incorrect or blending parameters are poorly defined, the system may make suboptimal assignments. For example, continuous blending can sometimes lead to **poor utilization of excavators** if a high-production shovel must be underfed for long periods to keep the overall blend within limits.

• **Constraint Hierarchy:** These blending-related rules function as powerful assignment constraints. They are part of the overall hierarchy of constraints considered by the system:

    ◦ **Material Compatibility Constraints** force shovels digging a material type to only feed dumps that accept that material.

    ◦ **Grade Restrictions** restrict trucks carrying a specific grade block from dumping at certain locations.

    ◦ **Grade Dump Locks** (especially with Hard Lock enabled) force trucks carrying that specific grade to tip at a single designated dump, overriding other truck or shovel locks.

• **Consequences of Configuration Failure:** If a discrete grade blend is incorrectly configured at a processor, or if the material type and grade do not match, the grade information may be ignored by the LP solution, leading to ineffective assignments. Failures in blending conformance can be flagged as **Blend Non-Compliance** exceptions, requiring Mine Controller intervention.

In summary, the utilities managing grades and blends provide the mathematical foundation for regulating material quality. Any oversight in defining or maintaining these parameters directly corrupts the operational model, resulting in poor assignment decisions, reduced productivity, inaccurate cycle times, and increased operational costs per ton.

--------------------------------------------------------------------------------

## Mining Grade Utility and Data Management

The sources describe the **Grade Utility** (also referred to as Grade Editor in some instances) as the essential tool used by mining operations, typically the **Ore Control Department**, to **create and maintain records for ore and waste material grades** within the system's database. This functionality is the fundamental precursor to enabling material management and blending activities controlled by the BlendMod Utility.

Purpose and Definition of Grades

A **grade** fundamentally describes the properties of the material to be mined, forming the basis for material classification, production assignments, and reporting [1, 2].

• **Comprehensive Description:** Multiple grades often combine to fully describe a single material type, encompassing properties like acidity, color, specific gravity, and sulfur content [1, 2].

• **Data Collection:** The office software automatically collects grade data when material is excavated by a loading tool [1].

Types of Grades

Grades are categorized based on the nature of the data they represent:

1. **Continuous Grades:** These have values that fall on a numerical scale with minimum and maximum boundaries, expressed in units such as percentage () or grams per tonne (textg/t) [3]. Examples include the percentage of Iron (Fe) or grams of Gold (Au) per tonne [3]. When configuring continuous grades in the Grade Editor, the user defines the numerical scale, unit type, and weight averaging method (typically Mass) [4].

2. **Discrete Grades:** These grades are characterized by non-numerical, categorical values ("either this or that") [3]. They typically define categories, royalties, or simple tags attached to mining blocks (e.g., Royalty = Fred, Source = Stockpile) [3]. Discrete grades are configured by defining the specific discrete values they can hold [4].

Creating and Updating Grades using the Grade Utility

The **Grade Utility** (or Grade Editor) is the primary interface for managing this foundational material data [5-7].

• **Role and Input:** Although grades are usually imported into the system from mine planning software, the **Builder** is typically responsible for manually creating or updating grades when new information is received [5].

• **Manual Creation Methods:** A grade record can be created in the utility by combining the **grade location** and **material type** to form the Grade ID (e.g., Cowspond Ore - 4) [8, 9]. Alternatively, mines can create unique, special Grade IDs [10].

• **Core Fields for Creation:** When creating a grade, the user must select its **Type** (Continuous or Discrete), input a **Name**, and select the **Weight Averaging Method** (usually Mass) [4]. For continuous grades, details like **Unit Type** and **Unit Name** () are defined, along with **Maximum, Desired, and Minimum** values [4].

The integrity of this data is crucial, as the Grade Utility provides the information used by later processes [8, 11, 12]:

• **Shovel Operation:** The grade ID must be recognized by the system when entered by shovel operators after a move or a change in material [8].

• **Dump Assignments:** Correct grade information ensures material goes to the proper dumping point [13].

• **Blending Input:** Grade records supply essential information (material characteristics, blend category, material quality values) for blend management [12, 14].

Context in Grade & Blending (The BlendMod Utility Link)

The records established in the Grade Utility are the direct inputs for the **BlendMod Utility**, which handles the complex logic of controlling material quality delivered to processing locations (crushers and stockpiles) [15].

• **Foundation for Blending:** Grades define the material qualities (like or categories like High Grade) that the BlendMod Utility uses to calculate blending parameters [16, 17].

• **Blend Constraint Enforcement:** The calculated grade information is used by the Linear Programming (LP) layer of the optimization system (Joptimiser) to enforce **blending constraints**, ensuring the total material produced within a defined interval meets the required average quality standards [18-20]. For example, continuous blending by component relies on the arithmetic averaging of the continuous grade values defined here to check if the delivered blend remains within set minimum/maximum percentage limits over a defined control mass [21-23].

• **Data Consistency:** If the system is configured for blend categorization, the grade must be properly defined so that the material type and grade match, preventing the grade information from being ignored by LP, which is vital for assignment decisions [24].

In essence, the Grade Utility is where the mine establishes the dictionary and parameters for the materials it moves, allowing the downstream systems, particularly the BlendMod Utility and the LP optimization algorithms, to accurately classify, track, and control the flow of those materials across the mine site [20, 25].

--------------------------------------------------------------------------------

## BlendMod Utility: Material Blending and Quality Control

The **BlendMod Utility** is a critical tool within fleet management systems like DISPATCH and Jmineops, specifically designed to **control material blending at crushers and stockpiles** (referred to generally as dumping points or sinks) to meet specified quality parameters [1-3]. This utility operates within the larger framework of **Grade & Blending**, relying on accurate material definition and optimization algorithms to manage material flow [4, 5].

Role and Function of the BlendMod Utility

The core purpose of the BlendMod Utility is to manage the quality and composition of material being delivered to processing locations (sinks) [1, 2]. The main menu of the utility typically presents three principal methods for configuring blending: **Continuous Blending by Component**, **Continuous Blending by Category**, and **Batch Blending by Component** [2, 6].

Context in Grade & Blending

Blending is fundamentally linked to **Grades**, which describe the properties of the mined material (e.g., acidity, sulfur content) [7, 8]. Grades can be **Continuous** (numerical values like percent or grams/tonne, used for blending components) or **Discrete** (categories or simple tags, used for blending by category) [6, 9]. Once grades are defined, they are associated with **Materials**, which specify the substances moved between destinations [10, 11]. The BlendMod Utility uses these established grades and materials to define the required blend recipe [12].

Key Blending Strategies Managed by BlendMod

The sources detail how the BlendMod Utility facilitates different blending strategies, primarily differentiated by _when_ the material must meet the quality specifications (continuously or only upon completion of a batch):

1. Continuous Blending (by Component or Category)

The continuous blending options leverage a **Control Mass** (tonnage) or **Control Interval/Duration** (time) over which the blend must remain within set limits [6, 12].

• **Continuous Blending by Component (Quality):** This method mandates that the flow of material must continuously adhere to blending limits based on specific material **qualities or components** (grades) over a defined control mass [6, 12, 13].

    ◦ For example, one could specify that every 4,000 tons (control mass) delivered to a crusher must contain a minimum of 64 percent and a maximum of 100 percent of Grade 1 [6, 14].

    ◦ To set up a parameter, the user enters minimum and maximum percentages for each blending component [14]. It is assumed the blend is currently being produced correctly, so users must enter a **Current** value that is halfway between the minimum and maximum defined limits [15, 16].

    ◦ **Impact of Control Mass:** Setting a larger control mass allows for greater temporary deviation from blending limits per truckload, whereas a smaller mass enforces stricter adherence [17, 18]. The minimum control mass should be at least five times larger than the mine's largest truck [18, 19].

• **Continuous Blending by Category:** This method regulates material flow based on defined **mining block categories**, often associated with material quality levels (e.g., High Grade, Medium Grade) [20, 21].

    ◦ The user specifies a **Target Percent (Target Pct)** for each category (e.g., 50% High Ore) and a **Percent Deviation**, which sets the allowable range around the target over the Control Mass [21, 22].

    ◦ Similar to component blending, the user must initially set the **Current Pct** values equal to the Target Pct to establish the assumption of continuous flow [23].

2. Batch Blending by Component

This method adopts a "cake-recipe" approach, only requiring the blend to meet specifications when the total **production target is achieved**, offering more short-term operational flexibility [24-26].

• **Goal:** The material production must meet blending limits only once the final target tonnage is achieved [24, 26].

• **Flexibility and Risk:** Batch blending allows excavators to be used efficiently, even if temporary output deviates from the final blend requirements [24]. However, this method is considered high risk because an equipment breakdown could prevent the necessary materials from being mined later, causing the final product to fail specifications [24].

Integration with Optimization and Monitoring

The configuration set in the BlendMod Utility is crucial because **blending constraints** are incorporated into the Linear Programming (LP) layer of Joptimizer/DISPATCH [5, 27, 28]. The LP algorithm ensures that the resulting allocation of trucks will achieve the required average quality within the specified minimum and maximum parameters [29, 30].

• **Assignment Impact:** The LP solution uses blending constraints to govern material flow, creating production circuits that adhere to the established quality targets [31-33]. If a truck is sent to a destination that violates current blending requirements, the assignment may be flagged as incompatible, preventing selection [34, 35].

• **Enforcement:** Joptimizer will automatically decrease feed rates to shovels that are contributing too much material if constrained by crusher capacity to meet blending requirements, potentially causing the high-production shovel to be underfed [17, 36].

• **Monitoring:** The blending constraints and their current status are monitored in locations like the **Processor Assistant** (Blend tab) to show if the current material percentages are conforming to the defined blend limits [37-39].

--------------------------------------------------------------------------------

## Continuous Blending Mechanisms in Mining Optimization

The sources define **Continuous Blending** as a method used within the mining optimization system to manage the flow of material to processing destinations, such as crushers or stockpiles, ensuring that the material composition remains within specified quality parameters over a determined control quantity or interval [1, 2].

This type of blending is primarily managed through the **BlendMod Utility** [3, 4].

Continuous Blending Mechanisms

The continuous blending process relies on tracking the qualities or categories of material dumped at a sink (destination) relative to predetermined limits [2, 5].

The two main types of continuous blending identified in the sources are:

1. Continuous Blending by Component (or Quality)

This method focuses on regulating the percentages of specific **material components** or qualities within the blend [5, 6].

• **Objective:** To ensure that the flow of material is continuously within set blending limits based on specific material qualities (components) over a defined control mass [2, 6]. Arithmetic averaging of grade values is used to calculate the actual grade qualities for continuous blending [7].

• **Example Parameters:** A user can specify that every 4,000 tons (the control mass) dumped at a crusher must consist of certain minimum and maximum percentages of specific grade components, such as Grade 1 or Grade 2 [2, 6, 8]. For instance, demanding a minimum of 1 gram to a maximum of 1.5 grams of gold per tonne over a set control quantity is an example of blending continuous qualities [5].

• **Implementation in BlendMod Utility:** When creating a blend parameter using this method, the user enters minimum and maximum percentage values for each blending component, and sets an initial value in the "Current" column halfway between the minimum and maximum to represent the desired blend currently being produced [9-11].

2. Continuous Blending by Category

This method focuses on managing the flow based on **mining block categories**, often associated with material quality [12, 13].

• **Objective:** To ensure that the ratios between different discrete grades (including material types) are delivered in the desired proportions over a defined control quantity (mass or time) [5, 14].

• **Categories:** Mining blocks may be categorized based on quality, such as High Grade, Medium Grade, and Low Grade ore categories [12, 13].

• **Example Parameters:** A blending parameter might specify that every 4,000 tons (control mass) must consist of 50 percent material from Category A blocks (high-grade ore), 25 percent from Category B, and 25 percent from Category C blocks [13].

• **Target and Deviation:** The user inputs the desired **Target Percent (Target Pct)** for each category and a **Percent Deviation** value, which defines the allowable deviation from the target percentages [15].

Context in Grade & Blending

Blending is a critical operation designed to ensure that material delivered to a sink (crushers or stockpiles) meets desired material composition and quality limits [14, 16].

**1. Grades (The Input Data):**Blending relies fundamentally on **Grades**, which describe the material to be mined [17]. Grades can be **Continuous** (numerical scale values like percent or grams/tonne, e.g., sulfur content or gold content) or **Discrete** (categories or simple tags, e.g., royalty or source type) [18, 19]. Grades must be created and linked to materials for blending to occur [20-22].

**2. BlendMod Utility (The Control Tool):**The **BlendMod Utility** is the interface used to define and manage these blending parameters [3, 4].

• **Control Variables:** Continuous blending options utilize a **Control Mass** (the tonnage that must meet limits) or **Control Interval/Duration** (the time over which the blend is computed) [2, 22-24]. The control mass/value should be at least five times the size of the largest truck to effectively satisfy blending and production requirements [25].

• **Assignment Logic:** The existence of blending constraints directly influences the Linear Programming (LP) optimization layer of the system [26, 27]. Joptimizer enforces these constraints to regulate material flow [28]. If constraints are set, LP will allocate hauling resources to achieve the required average quality within the specified minimum and maximum quality parameters [5, 29].

• **Impact of Control Mass:** Setting a large control mass allows for greater deviation from blending limits per truckload, while a smaller mass requires less deviation [9, 30]. However, the continuous blending methods carry a drawback: they can sometimes result in poor utilization, potentially leaving a high-production shovel underfed to stay within blending limits [30].

In contrast to **Continuous** blending, the alternative concept of **Batch Blending by Component** only requires the blend to meet specifications once the production target (target production tonnage) is fully achieved, allowing for greater flexibility in short-term excavator use, provided the blend is completed [31-34].

--------------------------------------------------------------------------------

## MineStar Fleet Assignment Logic and Monitoring

The sources thoroughly describe **Assignment Behaviors and Monitoring** within the Cat MineStar System Fleet (including DISPATCH and Joptimiser/Jigsaw), emphasizing that accurate data, proper configuration, and continuous monitoring are critical for the system's ability to achieve production goals and minimize costs. The core goal of the assignment engine is to **maximize overall production** and **minimize cost per ton** by efficiently directing haul trucks [1-3].

The system achieves this through a multi-layered optimization approach (Best Path/BP, Linear Programming/LP, and Dynamic Programming/DP) that relies on specific real-time behaviors and monitoring tools [1, 3-7].

Core Assignment Engine Logic

The foundation of assignment behavior rests on the optimization strategy, which seeks to minimize each component of a haul cycle to increase the overall number of cycles [1, 3].

1. **Optimization Models:** The system uses three primary models [3-6, 8]:

• **Best Path (BP):** Calculates the shortest travel route between any two points based on criteria like **shortest distance**, and feeds these routes and initial travel times to the LP model [4, 5, 8-10]. BP wakes up and recalculates only when there is a change in the road network (e.g., a road opens or closes) [11].

• **Linear Programming (LP):** Creates a theoretical "master plan" called the **LP solution**, which optimizes production circuits (paths and flow rates) based on constraints like digging rates, dump capacities, blending needs, and truck availability [1, 2, 8, 12-15]. The LP solution is calculated automatically every hour (or every 30 minutes in some older configurations) and is triggered by event changes like material or shovel status changes [16-20].

• **Dynamic Programming (DP):** Takes the LP solution and **enforces it in real time** by generating specific truck assignments (the list of instructions telling the truck where to travel) [21-26]. DP works to achieve "balance and synchronization" while adhering to the LP flow rates, prioritizing the least amount of "lost tonnage" (lowest cost) [24, 27-30].

2. **Assignment Decision Factors (Inputs):** The DP model considers numerous inputs, especially cycle component times, which must be accurate to ensure the best assignment decisions [31-33]. Key inputs include:

• **Cycle Time Components:** Travel Empty/Loaded (rolling average based on road segments, updated frequently), Queuing Empty/Loaded (based on truck monitoring and configured tolerances), Spotting Empty/Loaded (based on fixed design time), Loading (weighted rolling average of previous loads if dynamic efficiency is on), and Dumping (design time) [31, 32, 34].

• **Constraints and Restrictions:** Production plans (goals and priorities), blending requirements, TKPH constraints, operator restrictions (locks, bars), and machine compatibility constraints [33, 35-40].

• **Delays:** The expected duration of delays is factored in, allowing the assignment engine to still send trucks to a loading unit if the travel time suggests the truck will arrive just as the delay ends [41-43].

Assignment Behaviors

Assignments are initiated by various triggers and managed through distinct types:

1. Assignment Types

The system uses a mix of automatic and manual assignments [21]:

• **Immediate Automatic:** Generated automatically based on triggers like a change in the truck’s load state (e.g., finishing a dump and entering the Traveling Empty state) [21, 44, 45]. This is the preferred mode of operation [21].

• **Scheduled Automatic:** Time-based assignments (e.g., for refueling or end-of-shift tiedown) enacted when specific time windows (Required At, Arrive Before, Arrive After) and load state criteria are met [21, 46, 47].

• **Immediate Manual:** A Controller overrides the system to force an immediate assignment [21].

• **Manual Scheduled:** An assignment triggered by a specified load state, allowing Controllers to plan the next action after the truck completes its current activity (e.g., manually setting a refueling assignment when the truck is Empty) [21, 48, 49].

2. Assignment Overrides and Hierarchy

The system uses a hierarchy of controls when determining assignments: **Manual assignments override both locks and restrictions** [50-52]. For automated assignments, locks and restrictions govern truck movement [37, 38, 40, 52, 53]:

• **Absolute Locks/Bars:** Rules that may **not** be removed or relaxed to permit an assignment (e.g., hard-locking a specific grade block to a dump) [54-56].

• **Provisional Restrictions:** Rules that **may** be relaxed to permit an assignment if necessary to keep the operation running [54, 55].

• **Lock Hierarchy:** Dump locks applied to a truck supersede shovel or grade locks [57-59].

3. Handling Assignment Failures

If the system cannot determine a valid assignment, or if a restriction is violated, it posts messages called **exceptions** [60-65]. Troubleshooting a failed assignment requires accessing the **Assignment Context** to identify the root cause, which can range from machine delays or incompatibilities to insufficient destination capacity or route path issues [66-69].

Monitoring and Data Integrity

Continuous **monitoring** is vital because the accuracy of inputs directly impacts the quality of assignment decisions and production efficiency [70-73].

1. Real-Time Monitoring Tools

Mine Controllers primarily rely on dedicated screens to monitor assignment behaviors:

• **Fleet Update Assistant (FUA) / Travel Progress Monitor (TPM):** These interfaces display the status of all equipment, show trucks with failed assignments (usually in red), and allow quick checks of machine availability [74-78].

• **Assignment Context:** This pop-up window details incompatibilities or reasons why a proposed assignment failed, or why a specific assignment was chosen over alternatives [66, 69, 79]. Reasons can include blending requirements, exceeding maximum TKPH, or scheduled assignment time conflicts [80, 81].

• **Trucking Indication:** This page shows the quantity of trucks relative to loading tools and is updated with every assignment, helping controllers identify over-trucked or under-trucked conditions by comparing the calculated optimal number of trucks (_Computed Max_) versus the _Current (Target)_ number [82-85].

2. Data Integrity and Correction

Controllers are directly responsible for ensuring data integrity, especially concerning production cycles and non-productive delays [86-88].

• **Impact of Poor Data:** Without accurate mine knowledge, the system may send poor assignments, leading to trucks queuing, idle loaders, misdirected loads, and ultimately, higher costs per ton [36, 61, 70, 89, 90]. Manual assignments also risk disrupting the DP logic and causing data integrity errors such as inaccurate dig rates, zero spotting/loading times, or excessive queuing [53, 91, 92].

• **Monitoring Cycles:** Controllers must regularly view and edit **Truck Cycle** and **Delay** records to correct missing or inconsistent information (e.g., incorrect payloads, undetermined activity, or unmatched delay times) [87, 88, 93-97].

• **Managing Delays:** Controllers monitor and manage delays (unproductive time) properly, noting that delays record time and affect the assignment engine [41, 98, 99]. Accurate delays with estimated durations are vital because the assignment engine uses them to factor availability into future plans [100].

• **Troubleshooting:** Diagnostic tools like the **Field Communications Tab** (to review raw TMAC network traffic), **Mine Model Query** (to check incompatibilities), and **Exception View** (to find issues with road network slope, speed limits, or missed waypoints) are used to diagnose and resolve persistent assignment issues [101-106].

--------------------------------------------------------------------------------

## Fleet Management Scheduled Assignments and Behavior

Scheduled Assignments are a distinct category of assignments used in fleet management systems like DISPATCH and Joptimizer to manage truck movements, particularly for future or non-production tasks, ensuring optimized operation and facilitating shift management. These assignments are categorized into **Manual Scheduled** and **Automatic Scheduled** assignments, each playing a specific role in overall **Assignment Behaviors and Monitoring**.

Types of Scheduled Assignments

The sources define two primary types of scheduled assignments, differentiating based on the initiation method and purpose:

1. **Scheduled Manual Assignments:** These assignments are explicitly set by the Mine Controller and are triggered by a specific **load state** of a truck, typically serving as the next assignment once a condition is met [1, 2].

2. **Scheduled Automatic Assignments:** These are generated by the Assignment Engine without immediate Controller intervention. They are enacted when a pre-configured **time window** can be met, and the truck's load state matches the criteria [3, 4]. These are typically time-based assignments designed for events planned for the future [2].

Core Mechanisms and Configuration

Scheduled assignments rely on specific configurations and inputs to function correctly:

1. Triggers and Conditions (Load State)

Manual scheduled assignments are triggered by a **load event** (like being Empty or Full) and serve as the immediate next assignment following that event [2]. For example, a scheduled manual assignment might be set for a truck to **refuel** only when its load state is **Empty** [5].

2. Timing Constraints (Automatic)

Automatic scheduled assignments primarily utilize timing constraints to govern when the assignment is executed, allowing for strategic planning:

• **Required At:** This is the **mandatory time target** the assignment must include, aiming for the truck to arrive as close to this time as possible [4, 6].

• **Arrive Before/After:** These optional constraints define the window within which the assignment must be executed [4, 7]. If the window is too narrow, the system may fail to generate the assignment [8, 9]. Omitting the **Arrive After** constraint might cause the assignment to trigger prematurely if the truck has no other destination planned [7].

The system uses a configurable **Scheduled Assignment Arrival Time** setting (e.g., Strict, Normal, Flexible) to determine the required accuracy of arrival relative to the `Required At` time [6, 10].

3. Operational Purpose and Examples

Scheduled assignments are frequently used for non-production activities critical to shift management and maintenance planning:

• **Refueling:** Automatic refueling assignments are optimized to generate production gains by reducing the frequency of fueling activities, ensuring trucks go for fuel when needed (e.g., when the fuel level drops below a set percentage) rather than at a fixed time [11, 12]. A key practice for refueling assignments is setting an **On Arrival Delay Type** to automatically trigger a delay when the truck reaches the fuel bay [13, 14].

• **Shift Change/Tiedown:** Scheduling logic is crucial for end-of-shift procedures to assign trucks to **tiedown** locations, maximizing production until the last moment [2, 15, 16]. The optimization engine analyzes whether a truck has time for "another cycle" before routing it to the shift change location, prioritizing the option that minimizes the time difference (delta) from the target time [8, 15].

Impact on Assignment Behaviors and Monitoring

Scheduled assignments significantly interact with the core optimization and monitoring functions:

• **Optimization Integration:** The assignment engine considers scheduled assignments (along with blending and TKPH constraints) when determining the best allocation for machines [17, 18].

• **Data Integrity Risk (Manual):** Manual assignments, including scheduled manual assignments, **disrupt the Dynamic Programming (DP) logic** and cycle time calculations [2, 19, 20]. This disruption can cause errors such as zero spot or load times, inaccurate travel times, inaccurate dig rates, and excessive queuing [19, 20]. To mitigate this risk, the suggested best practice is to set a manual assignment as a **"pre-assignment"** when the truck is still loaded during its haul cycle, as the DP model handles this scenario more accurately [2, 21, 22].

• **Monitoring and Status:** Scheduled assignments are monitored in interfaces such as the **Scheduled Assignment Monitor** [23]. Assignments currently in progress are marked as **Active (Green)**, while those deemed impossible to achieve due to time constraints are marked as **Late (Red)** and remain listed [9, 23].

If a machine is delayed due to an "assignment delay," the truck will still be factored into the Assignment Engine's calculation, even if it is temporarily excluded from assignment consideration (non-assignment delay) [24, 25]. The system will typically anticipate the end time of a delay and factor this into future assignments, demonstrating the predictive nature inherent in handling scheduled events [26].

--------------------------------------------------------------------------------

## Scheduled Mining Assignments Triggered by Load State

The sources highlight that assignments being **Triggered by Load State** are a key feature of **Scheduled Assignments**, particularly in determining the next steps for a truck after completing a major production activity, such as loading or dumping. This mechanism allows the mining system (like DISPATCH or Joptimizer) to manage transitions between productive hauling and necessary non-production activities efficiently.

Load State as a Trigger in Scheduled Assignments

Scheduled assignments, whether manual or automatic, often depend on a specific load state to activate, ensuring the assignment is relevant to the truck's current condition:

• **Definition:** A scheduled assignment is defined with a specific **Load State** (e.g., **Empty** or **Full**) that the truck must be in for the assignment to be enacted [1-3].

• **Manual Scheduled Assignments:** These assignments are explicitly triggered by a specific load state and are typically intended as the **next assignment** following the completion of a condition [4, 5]. For instance, a Mine Controller might require a specific manual directive to be actioned, such as picking up a load from a designated location or getting fuel, once the truck is in the correct load state [4].

    ◦ In the **Scheduled Assignment Editor**, the Manual option limits the assignment to load state triggers, where the user selects the **Action** (e.g., To refuel, To load), the necessary **Load State** (e.g., Empty), and the specific destination (e.g., Fuel Bay) [2].

• **Automatic Scheduled Assignments:** These are typically time-based and intended for future events, but they are also constrained by the load state [1, 5-7]. The system automatically triggers the assignment when a pre-configured time window can be met, _and_ the truck's load state matches the given criteria [1, 7].

Example: Automatic Refueling Assignments

A primary example of an assignment triggered by a load state is **automatic refueling**:

1. **Load State Requirement:** Joptimizer **only considers empty trucks for fuel assignments**, ensuring a truck does not go to a fuel station while loaded [8, 9].

2. **Assignment Trigger:** The need to refuel is determined by the truck's **current fuel tank level** reaching a pre-configured threshold, such as the **Fuel Low Percent** [9-11].

3. **Assignment Logic (Load State Integration):** If the fuel level drops below the initial threshold (e.g., 25%), Joptimizer starts considering assignments that are "on the way to or in the vicinity of a fuel station" [11, 12]. The truck is then assigned to the fuel station **once the truck dumps and requires an assignment** (i.e., enters the empty load state) [13, 14].

4. **Action at Assignment Editor:** When creating a scheduled assignment for refueling, the **Load State** is set to **Empty** [2, 3]. Best practice for refueling assignments is also to define an **On Arrival Delay Type** so that a delay is automatically triggered once the truck reaches the assigned fuel bay [3, 15].

Consequences and Monitoring

Using the load state as a trigger ensures operational coherence:

• **Data Integrity:** This mechanism is built on the foundation of accurate data collection throughout the haul cycle, as the system relies on the truck transitioning between load states (e.g., _Travelling Empty_ to _Loading_ and _Hauling_ to _Dumping_) [16, 17].

• **System Tracking:** The scheduled assignment system tracks these pending assignments, allowing controllers to monitor their status (e.g., Active, Inactive, Late, Finished) in the **Scheduled Assignment Monitor** [18, 19].

• **Manual Override:** Even when scheduled assignments are set, a manual assignment can be initiated to override these rules, provided the truck is assigned to a location that accepts the material it is currently carrying [6, 20, 21].

--------------------------------------------------------------------------------

## Timing Constraints in Scheduled Automatic Assignments

The sources indicate that **Timing Constraints** such as **Arrive After, Required At, and Arrive Before** are essential parameters used primarily within **Scheduled Automatic Assignments** to manage and optimize non-production or future events for haul trucks. These constraints enable the Assignment Engine (Dynamic Programming/DP) to strategically route trucks to meet specific arrival targets, thereby maximizing productivity leading up to that scheduled event.

Function and Components of Timing Constraints

Timing constraints are core to defining a Scheduled Automatic Assignment, which is typically time-based and intended for future events like shift change, refueling, or planned maintenance, [1], [2].

The three specific temporal parameters utilized are:

1. **Required At:** This is the **mandatory time target** that is **always included** in the definition of a Scheduled Automatic Assignment [3], [4], [2]. The Assignment Engine aims to get the truck to the destination as close as possible to this time [3].

2. **Arrive Before:** This defines the latest time the Assignment Engine should attempt to get the truck to the destination [3], [5]. This time target is considered optional but provides bounds for the assignment [4].

3. **Arrive After:** This defines the earliest time the truck should arrive at the destination [3], [5]. This target is also optional, but omitting it might cause the scheduled assignment to trigger too early if the truck has no other destination to go to [5], [4].

Using these constraints, the Controller sets the intended arrival time target, and the optimization algorithm determines the most optimal path and trigger time for the truck to receive its assignment [6], [2].

Context in Scheduled Assignments

Scheduled assignments are one of the key types of assignments, distinct from immediate automatic and immediate manual assignments [7].

• **Goal of Optimization:** When these timing constraints are set, the goal is to **optimize production outcomes** by allowing trucks to continue hauling material until the very last moment before they must report to the scheduled location [6]. The system looks at the time delta (difference) between travel time plus cycle time, and the scheduled `Required At` time, selecting the option that minimizes this delta [8], [9].

• **Accuracy Levels:** The optimization system uses a configurable **Scheduled Assignment Arrival Time** setting in the Assignment Supervisor to define the required accuracy of arrival relative to the `Required At` time [6].

    ◦ **Strict:** Trucks aim to arrive within 5 minutes of the required time [4].

    ◦ **Normal:** Trucks aim to arrive within 10 minutes of the required time [4].

    ◦ **Flexible:** Trucks aim to arrive within 20 minutes of the required time [4].

• **Triggers:** Scheduled assignments are automatically triggered by the system when a pre-configured time window can be met, provided the truck’s load state matches the criteria (e.g., `Empty` for refueling) [1], [7]. They can also be triggered by a specific assignment request or a reassignment waypoint [5].

• **System Behavior (Example: Shift Change):** For events like shift change, using `Arrive Before` is often the preferred strategy, while using `Required At` might cause the system to attempt an extra cycle if the resulting arrival time (post-cycle) is closer to the `Required At` time than going directly to the tie-down location [8], [9]. If the constraints (especially the window defined by `Arrive After` and `Arrive Before`) are too narrow, the system may fail to generate an assignment [8], [10].

The system generally includes the option to define an **On Arrival Delay Type** when creating scheduled assignments, which is considered a best practice, such as for refueling activities [11], [5].

Monitoring Failures

When timing constraints are violated, the assignment may fail, impacting fleet operations:

• **Assignment Failure:** If the truck is projected to arrive at the scheduled destination **outside the Arrive After or Arrive Before window**, the assignment context will list this as a reason the compatible destination was not chosen [12].

• **Monitoring Status:** Scheduled assignments are displayed on the **Scheduled Assignment Monitor** [13]. Assignments that the system deems impossible to achieve due to time constraints will be marked as **Late** (Red) and remain in the monitor list [13].

--------------------------------------------------------------------------------

## Manual Circuits: Chaining Scheduled Truck Assignments

The sources discuss the creation of **manual circuits** through the mechanism of chaining **Scheduled Manual Assignments** in a sequence. This approach is positioned within the broader context of both manual and automatic assignment control, offering dispatchers fine-grained control over specific movements when automatic optimization is insufficient or undesirable.

Definition and Purpose of Manual Circuits

A manual circuit is essentially a predefined sequence of assignments that a Mine Controller constructs for a truck to follow to accomplish a specific operational goal. The sources indicate that one approach to creating manual circuits is by utilizing the **Schedule** section in the **Scheduled Assignment Editor** [1].

The purpose of creating scheduled assignments, which form these circuits, is to allow the Controller to enforce a specific manual directive _after_ a truck completes its current activity, such as picking up a load from a specific location or obtaining fuel [2]. This is particularly useful when there is a determined need for a follow-up assignment to address an issue or concern [2].

Mechanism of Creating Manual Circuits

To create a manual circuit (or a sequence of assignments) for a truck:

1. **Chaining Assignments:** Multiple scheduled manual assignments can be defined for the same truck in the **Scheduled Assignment Editor** [3]. The **Schedule** section allows a sequence of assignments to be created by defining the first assignment, clicking 'Add', and repeating the action for subsequent assignments [1].

2. **Triggering by Load State:** Scheduled manual assignments are triggered by a specific **load state** of a truck and are typically intended as the _next_ assignment [2], [4], [5].

3. **Assignment Details:** The dispatcher specifies the **Action** (e.g., To load, To refuel), the necessary **Load State** the truck must be in (e.g., Empty, Full), and the specific **Location** for the activity (e.g., Fuel Bay or Processor) [3].

4. **On-Arrival Delay:** A valuable feature of scheduled assignments is the option to automatically trigger a delay once the truck reaches the assigned destination (**On Arrival Delay Type**), which is considered best practice for refueling assignments [6].

This type of assignment can also be performed via the **Truck Assistant** page using the "when next..." options [4], [5].

Context within Scheduled Assignments (Manual/Automatic)

Manual circuits contrast with the standard assignment behavior driven by the system’s primary optimization models (Linear Programming/LP and Dynamic Programming/DP). The assignment system distinguishes between three types of assignments:

1. **Immediate Automatic Assignment:** Issued by the Assignment engine without Controller intervention, typically triggered by a change in the truck’s load state [7].

2. **Scheduled Automatic Assignment:** Enacted when a pre-configured time window can be met, and the truck’s load state matches given criteria [7], [8]. These are time-based assignments, designed for future events, where the controller sets **time targets** (Arrive After, Required At, Arrive Before) to ensure the truck arrives near a specific time [5], [9].

3. **Scheduled Manual Assignment (Circuits):** These are explicitly set by the Controller and are typically triggered by a load event, acting as the immediate next assignment following a condition being met [7], [4].

The use of manual assignments (including scheduled manual circuits) provides the dispatcher with flexibility but introduces risks. **Manual assignments disrupt the DP logic** and cycle time calculations, potentially causing data integrity errors such as zero spot or load times, inaccurate travel times, inaccurate dig rates, or queueing [10], [11]. If a manual assignment must be executed, the best practice suggested is to set it as a **"pre-assignment"** when the truck is still on the loaded side of the haul cycle, as DP handles this more accurately [12], [13].

Manual circuits and scheduled assignments, overall, ensure that critical non-production tasks (like refueling or shift change procedures) occur optimally or exactly when required by operational necessity [2], [5]. For example, the system uses scheduling logic to decide whether a truck has enough time to complete "another cycle" before being routed to a designated **tiedown** location at a "Required At" time for a shift change [14].

--------------------------------------------------------------------------------

## Haul Truck Cycle Statuses and Fleet Optimization

The concept of **Truck Haul Cycle Statuses** is central to the operation of dynamic fleet management and optimization systems like DISPATCH and Jmineops/Joptimizer. These statuses define the sequence of activities a truck undergoes between hauling a load and dumping it, providing the fundamental real-time data needed for accurate **Assignment Behaviors and Monitoring** to maximize productivity and minimize costs [1-7].

The full production cycle for a haul truck runs from **Traveling Empty** to **Dumping** [2, 3, 8]. The system continuously monitors and records the time spent in each phase, making the entire haul cycle a sequence of measured and often automatically triggered activities [1, 5].

Key Truck Haul Cycle Statuses and Triggers

The core activities of a typical haul cycle and their defining triggers are consistently detailed across the sources:

1. Traveling Empty (Beginning the Cycle)

• **Trigger:** The activity starts automatically **when the bed is fully down after dumping** or when the truck status changes to **Ready** [9-13]. This transition immediately prompts the office software to provide a new assignment to a loading unit [9, 10, 13].

• **Monitoring During Travel:** If a truck stops for **less than two minutes**, that time is typically absorbed as **Traveling** to account for stops at intersections [14-18]. If the truck stops for **more than two minutes**, the operator is prompted to enter a **Delay**, **Down**, or **Standby** reason [14, 18, 19]. Misroute detection (traveling outside the assigned path by a configured distance, typically 100 meters) during this phase also triggers a reassignment request [19-23].

2. Arriving Empty (Awaiting Service)

• **Trigger:** The truck transitions to **Arriving Empty** when it **passes through the entry waypoint at the destination of the assigned route** [9, 13].

• **Transition to Waiting:** This phase is immediately followed by the **Waiting** or **Queuing Empty** activity. This starts when the truck stops within the defined waiting radius of the assigned shovel [24-27]. If traffic management is highly configured, queuing can be triggered when the truck slows to **0.5 meters per second** and passes a specific face waypoint (typically the second to last) [13, 24, 28]. If the truck moves inside the waiting radius, that time is typically still considered **Waiting** [29, 30].

3. Spotting (Positioning for Load)

• **Trigger:** The truck enters the **Spotting** activity when the **GPS detects that the truck has reversed its heading** within the shovel’s defined spotting distance [31-33]. This activity captures the final positioning movements before loading starts [13, 34].

• **Measurement:** The time spent waiting under the loader for the first bucket is absorbed as **spotting time** [35, 36]. This activity typically occurs only in the office software and may not appear on the operator's screen [34, 37]. The end of spotting is defined by the start of the Loading activity [35, 36].

4. Loading (Receiving Material)

• **Trigger:** Loading starts when the **first or second bucket of material is placed into the bed of the truck**, detected by changes in **payload and strut pressures** [9, 13, 32, 38-41]. For trucks without payload systems, the operator can manually press a **Start Load button** [41, 42].

• **End of Loading:** The activity ends when the truck has **traveled more than the departure distance** from the loading point (e.g., 25 meters), or when a **kickout signal is received from the shovel operator**, triggering the dump assignment [21, 28, 43-47]. Importantly, operators **cannot enter delays** while reporting the Loading activity [44, 48].

5. Traveling Loaded (Hauling)

• **Trigger:** The truck enters the **Hauling** activity when it moves more than the **departure distance** from the loading point, or when the shovel operator signals the end of the load [45, 49, 50]. This action triggers Jmineops to generate the correct dump assignment based on material type, LP solution, and restrictions [45, 49, 50].

• **Monitoring During Travel:** Similar to Traveling Empty, stops of less than two minutes are absorbed as **Hauling** [16, 18]. Longer stops trigger delay prompts [16, 18].

6. Queued at Dump, Backing, and Tipping (Unloading)

• **Queued at Dump:** Occurs when a loaded truck stops inside a dump boundary (e.g., 0 km/hr or $<5$ km/hr), recording time spent waiting before tipping [51, 52]. This queuing ends when the tipping switch activates or the truck moves for more than three seconds [53, 54].

• **Backing:** Begins when a truck in Hauling or Queued activity starts reversing its heading within the dump boundary [55-57].

• **Tipping/Dumping:** Follows backing when the truck stops inside the tipping radius [56-58]. Tipping duration has a configurable maximum of three minutes [59, 60]. The truck's **bed-down** status signals dump completion, which automatically transitions the truck back to the **Traveling Empty** state, restarting the entire cycle [10, 12, 13, 61, 62].

Role in Assignment Behaviors and Monitoring

The accurate measurement of these haul cycle statuses is the backbone of the system's optimization and monitoring capabilities:

• **Assignment Engine Logic:** The primary goal of the Assignment Engine (LP/DP models) is to **minimize each component of the haul cycle** to increase the overall number of productive cycles and minimize costs [63-65]. The duration of activities like Traveling Empty/Loaded, Queuing, Spotting, Loading, and Dumping are used as factors when determining assignments [3, 66, 67].

• **Real-Time Calculations:** The duration of many of these activities (especially loading, spotting, and traveling times) is dynamically calculated using rolling averages (e.g., typically based on the last four loads/trips) [67-69]. This constant recalculation informs the Best Path (BP) model and the Dynamic Assignment (DA) layer to make optimal real-time decisions, such as which shovel is the **neediest** (least lost tons) [70-72].

• **Exception Management and Data Integrity:** Deviations from expected cycle states generate exceptions (e.g., **Misroutes** or trucks running **Late**) that alert the Mine Controller for immediate intervention [73-80]. Errors in capturing status data (e.g., zero second load times or incorrect delays) directly lead to **inaccurate cycle times**, which corrupt the rolling averages used by the optimization models and result in poor assignments, excessive queuing, and idle equipment [69, 71, 76, 81-88].

• **KPI Reporting:** The duration of cycle components is aggregated into critical performance metrics like **Shovel Dig Rate**, **Shovel Tons per Hour**, and **Average Truck Queue Time**, which are monitored via KPI dashboards and reports to continuously track the efficiency of the mine [89-96].

--------------------------------------------------------------------------------

## Haul Cycle Dynamics: Traveling Empty Phase

The sources clearly define **Traveling Empty** as the initial, critical phase of the truck production cycle following the dumping activity, where the empty truck proceeds toward a loading unit to receive its next load.

Trigger and Definition

The Traveling Empty status is specifically triggered by the completion of the dumping process:

• The activity officially starts **when the bed is fully down after dumping** [1-3].

• This transition to the **Traveling Empty** state immediately **prompts the office software to provide an assignment** to the truck [1-3].

• Empty trucks automatically enter this activity after the status changes to Ready or **after departing from a dump** [4, 5].

In the context of the complete haul cycle, the Traveling Empty phase represents the beginning of one complete circuit [1, 6]. The full cycle generally moves through [2, 6]: Traveling Emptyrightarrow Arriving Emptyrightarrow Loadingrightarrow Traveling Loadedrightarrow Arriving Loadedrightarrow Dumping.

Duration and Travel Dynamics

The length of time a truck spends Traveling Empty is a crucial data point used by the optimization engine:

• **Calculation for Assignments:** The assignment engine uses the **individual road segments rolling average** travel time for empty vehicles when determining assignments [7]. This rolling average is typically updated every 1.5 minutes (by default) based on duration, truck class, and load state [7].

• **Best Path Utilization:** During this activity, the truck should follow the **best path** (BP) calculated by the system, which is represented as a green line on the operator's display [8, 9].

Potential Disruptions and Status Transitions

While in the Traveling Empty status, the truck is continuously monitored, and several events can alter the status or trigger a reassignment:

1. **Stops and Delays:** If a truck stops while traveling empty, the behavior depends on the duration:

• If the truck stops for **less than two minutes**, the time spent stopped may be **absorbed as traveling** (to account for stops at intersections) [9, 10].

• If the truck stops for **more than two minutes**, the operator should be prompted to enter a reason (Delay, Down, Standby) [9, 10]. If configured, the truck may enter an **auto-standby** if it has not moved for a period of time (default is 15 seconds) [10-12].

2. **Misroutes and Reassignment Triggers:**

• If the truck deviates from the assigned route (e.g., travels over 100 meters outside the assigned path), a **misroute exception** is sent to the Mine Controller, invalidating the current assignment and requesting a new reassignment [8, 9, 13].

• The assignment may also be **rechecked at a reassignment waypoint** (callpoint) [14-17]. If conditions have changed (e.g., shovel delay or slow digging), the system may **reroute the truck to a different shovel** if a better alternative is identified without requiring the truck to backtrack [18-20].

3. **Arrival and Next Activity:** The Traveling Empty phase ends when the truck **passes through the entry waypoint at the destination** of the assigned route, initiating the **Arriving Empty** state [1-3].

In essence, the accuracy of the Traveling Empty time and path is fundamental, as it influences the overall haul cycle time, fuel calculation [21], and the scheduling logic employed by the optimization engine to ensure minimal idle time across the fleet [22].

--------------------------------------------------------------------------------

## Truck Haul Cycle: Arriving Empty Status and Triggers

The event of a truck **"Arriving Empty"** is a crucial segment within the overall **Truck Haul Cycle Statuses**, marking the transition from road travel to the stationary activities associated with loading. This status change is triggered specifically when the empty truck reaches a pre-defined point near its assigned destination (typically a loading unit).

Definition and Trigger Mechanism

The "Arriving Empty" activity occurs when a truck successfully completes the "Traveling Empty" segment of the haul cycle and reaches its intended location, prompting the transition to loading preparations [1-3]:

• **Trigger Location:** The change to the "Arriving Empty" state is triggered when the truck passes through the **entry waypoint at the destination of the assigned route** [1-4].

• **Haul Cycle Progression:** A truck enters the "Traveling Empty" state after its truck bed is fully lowered following dumping, which prompts the office software to provide a new assignment [2-4]. Upon reaching the designated entry waypoint, the status transitions to "Arriving Empty."

In the DISPATCH system (and related optimizers like Jigsaw), this arrival event signifies that the truck is moving toward becoming productive, ready to proceed to the subsequent activities of queuing and spotting before finally loading [2-5].

Context within the Truck Haul Cycle

The "Arriving Empty" status is immediately followed by activities that measure non-productive time leading up to loading:

• **Queuing (Waiting):** The truck then enters the **"Waiting at shovel"** or **"Queuing Empty"** activity, which starts when the truck stops within the set waiting distance (radius) of its assigned shovel [6, 7]. If the area is configured for queuing, the truck will enter the queuing state when it slows to 0.5 meters per second and passes a designated waypoint (typically the second to last) [2, 8, 9].

• **Spotting:** Following queuing, the truck proceeds to the **"Spotting"** activity, where it positions itself under the shovel, often involving reversing [2, 8-10].

• **Loading:** The cycle progresses to **"Loading"** once the first or second bucket of material is placed into the truck bed, often detected via payload sensor pressures [2-4].

Consequences of Missing the Trigger

Accurate completion of the "Arriving Empty" status is essential for accurate data and operation:

• **Manual Correction:** If a truck misses the entry waypoint at the destination, the state change would have to be **manually set** by the operator or Mine Controller [11, 12].

• **Dynamic Reassignment:** The "Arriving Empty" event is a necessary step that validates the truck is progressing along its assigned path. While reassignments often occur at intervening call points, the destination trigger ensures the system is aware the truck has arrived and is ready for the next phase of the cycle [13, 14].

--------------------------------------------------------------------------------

## Defining Mine Truck Loading Activity and Triggers

The sources provide a precise definition of the **Loading** activity within the **Truck Haul Cycle Statuses** for mine management systems like DISPATCH and Jigsaw/Joptimiser, detailing how this key productive activity is triggered, measured, and monitored.

Definition and Triggers of the Loading Activity

Loading is a core productive activity in the haul cycle, immediately following the spotting activity, where the truck receives material from the loading unit. The start of the loading activity is specifically defined by payload detection:

• **Primary Trigger (Payload Detection):** Loading starts when the **first or second bucket of material is placed into the bed of the truck**, which is detected based on the **payload and strut pressures** transmitted by the truck's weighing system (e.g., VIMS or TPMS) [1-8].

• **Alternative Triggers (Without Payload System):** For trucks without a dedicated weighing system, the start of loading is reported when the **Start Load button is pressed** on the operator panel [9]. The system can also be configured to detect loading automatically based on proximity and movement (e.g., when speed reaches 0 within the shovel radius), but this GPS-based mechanism can be problematic, potentially causing "spurious start of load events when trucks stop multiple times while spotting" [7]. If the `loading_distance` is set to zero, the start of load detection based on GPS is disabled for trucks with a working payload system [7, 10].

• **System Logic:** Loading begins after the truck has stopped within the **loading radius** of its assigned shovel after traveling in reverse (or forward, if configured) [7, 8, 11]. This transition occurs from the **Spotting activity** [7, 12].

The duration of the **Loading** activity is defined as the time from when the first bucket of material is placed into the bed until the moment the truck is considered **Full** (or the end of loading is signaled) [13].

Monitoring the End of Loading

The system needs to accurately capture the **end of loading** to trigger the next stage of the haul cycle, which is assigning the truck to a dump destination. End of loading detection is triggered by several mechanisms:

• **Departure Distance:** The end of loading is reported when the GPS detects that the truck has traveled **more than the departure distance** from the loading point [10, 14-16].

• **Shovel Signal/Kickout:** The activity ends with a **Kickout signal from the shovel** (if fitted) or when the shovel operator signals that the truck bed is **Full** [4, 17, 18]. The end of loading for the truck generates the **dumping assignment** [19].

• **Speed/Movement:** The activity ends if the truck is traveling at a speed greater than or equal to 15 kph (or moves for more than 10 seconds at a speed greater than or equal to 4 kph) or leaves the queue or loader zones while moving [10, 14, 17, 18].

Context within Truck Haul Cycle Statuses

The Loading activity is a crucial measured component in the overall haul cycle, which begins when the truck is empty and ends when it dumps its load, restarting the cycle [20-24].

• **Productivity Calculation:** The time spent loading is critical for calculating shovel productivity metrics, such as **Shovel Dig Rate** and **Shovel Tons per Hour**, which directly influence Joptimizer's assignment logic [25, 26].

    ◦ **Load Time (Haul Cycle):** Calculated as the time from when the truck operator pressed "First Bucket" until the shovel operator signals the truck is "Full" (kicked out) [13].

    ◦ **LP Calculation:** For assignment computations, the time allocated for loading is based on a **weighted rolling average of the last four loads** if the Dynamic Efficiency factor is enabled, or based on the configured load time adjusted by an efficiency factor if disabled [27, 28].

• **Activity Sequence:** The haul cycle progression moves sequentially through defined states: Traveling Empty, Arriving Empty, Queuing, Spotting, **Loading**, Traveling Loaded, Arriving Loaded, and Dumping [1, 22]. Incorrect activity reporting (such as zero second loading times or failure to press the necessary button) disrupts the historical data integrity and can lead to inefficient assignment decisions [9, 29, 30].

• **Non-Productive Time Absorption:** Notably, any time spent waiting beneath the loader for the first bucket after spotting is absorbed into the **Spotting time**, not the Loading time [12, 31]. Furthermore, operators cannot enter delays while a truck is reporting the **Loading** activity [17, 18].

--------------------------------------------------------------------------------

## Truck Haul Cycle Queuing Definition and Measurement

The sources define **Queuing** as a specific non-productive activity that occurs during a truck's haul cycle, typically triggered when a truck slows down to a very low speed near a destination,.

Definition and Triggers of Queuing

In the context of **Truck Haul Cycle Statuses**, queuing represents the time a truck spends waiting to be loaded or unloaded, which is tracked and recorded to monitor efficiency and operational time usage,.

• **Trigger at Loading Unit (Shovel):** Queuing is specifically triggered when a truck slows down to **0.5 meters per second** (m/s) and passes a face waypoint (typically the second to last waypoint) while traveling toward a loading unit [1], [2].

• **Alternative Name/Trigger:** The alternative status of **Waiting to Spot** may appear onboard as "Queuing" if configured, and this state occurs when the truck has stopped and is waiting to spot to the loading tool [3], [4], [5].

• **Queueing at Dump:** The sources explicitly note that queuing also applies at the dump destination [6], [1], [7].

    ◦ **Trigger at Dump:** A truck in the **Hauling** activity inside a dump boundary enters the **Queued** activity when its GPS position detects that the truck has stopped (usually defined as **0 km/hr** or as configured, e.g., **< 5 km/hr**), [8], [9].

Queuing as a Measured Activity

Queuing is a non-productive, operational time [10]. It is differentiated from productive activities (like loading or traveling) and intentional downtime (like delays or standby) [10].

• **Automatic Capture:** Queuing is captured in the system through **automatic triggers** and provides accurate queue time numbers to the site, making attempts by operators to enter delays for queuing unnecessary [10].

• **No Expected Completion Time:** Unlike many other haul cycle activities (which have expected completion times/ETAs), queuing (for both trucks and shovels) is a **measured activity** that does not have an expected completion time [11], [9].

• **Duration Measurement:** The time spent queuing is critical data. When a truck stops inside the waiting distance (radius) of its assigned shovel and waits, the start of queuing is recorded immediately. The end of the waiting/queuing period is recorded when the truck begins **Spotting** (positioning under the shovel) [12], [13].

Impact on Assignment Behaviors and Monitoring

The accurate measurement and reporting of queuing time is paramount for the overall performance of the optimization system:

1. **System Monitoring and Decision Making:** Queuing time is a crucial input for the assignment engine, specifically the Dynamic Programming (DP) model, as excessive queuing indicates inefficiency [14], [15], [16]. Queuing time directly affects the overall **cycle times** [17], [18].

2. **Assignment Decisions:** The optimization system monitors the **expected queuing time** and the number of queued trucks to adjust assignment decisions accordingly [19]. Queuing time is a component of the total cycle time used in calculating the number of required trucks for a given production circuit [20].

3. **Troubleshooting Over-trucking:** High truck queue times are an indication of **possible over-trucking** at the loading unit, alerting Controllers that trucks may need to be reallocated or parked [21], [22].

4. **Minimizing Costs:** By identifying and minimizing queuing time, the system aims to reduce costs per ton and maximize overall production efficiency [14], [23], [24].

The duration of the queuing activity at the dump is specifically defined to end when the dump switch (tray sensor) activates or the truck moves for more than three seconds, signaling the transition to the **Backing** or **Tipping** activity [8], [25]. If a dump switch fails, the decrease in payload weight serves as a backup mechanism to signal the end of queuing [26], [27].

--------------------------------------------------------------------------------

## Truck Haul Cycle: Definition and Analysis of Spotting

The sources define **Spotting** as a specific and critical activity within the larger context of a truck's **Haul Cycle Statuses** in a dynamic fleet management system like DISPATCH or Jigsaw/Jmineops. Spotting refers to the time period when a truck is actively positioning itself to receive a load or dump its material.

Definition and Characteristics of Spotting

Spotting is a defined activity that captures the time spent positioning the truck at the load or dump site:

1. **Spotting at the Loading Unit (Shovel):** Spotting is defined as the moment when the truck is **reversing until loaded** or performing the last movement out of the queue to get into position under the shovel or loading tool. The time spent sitting under the loader waiting for the first bucket is absorbed as **spotting time**.

2. **Spotting at the Dumping Point (Dump/Crusher):** Spotting also applies at the dump destination.

3. **System Recording:** Spotting occurs internally within the office software and typically **will not appear on the operator's screen** as a separate state, although the related status time is recorded.

Triggers and Transitions for Spotting

The entry into the Spotting activity is triggered by specific actions, indicating that the truck is transitioning from waiting/traveling to active loading preparation:

• **Trigger from Waiting:** Trucks already in the **Waiting activity** enter the **Spotting activity** when the GPS detects that the truck has reversed its heading within the **spotting distance** of a shovel.

• **Reverse Movement:** Spotting starts when the truck begins **backing into the shovel** and moves into the spotting activity. Jigsaw uses specific configuration parameters, such as a directional change greater than the **Default Reverse Heading of 120 degrees in less than 2 seconds**, to automatically detect the start of backing/spotting.

• **System Logic (Loading):** The truck has to have either stopped in the **waiting radius** or in the **loading radius** in order to report spotting. The truck will only change to spotting if there is not already another truck being loaded. If another truck is still being loaded, the new truck will continue to report **Waiting** or **queuing**.

• **End of Spotting:** The end of the spotting activity is reported when the **Loading activity** starts.

Importance within the Haul Cycle and Optimization

Spotting time is a crucial metric directly influencing overall cycle times, assignment decisions, and reporting:

1. **Cycle Time Calculation:** Spotting time is one of the individual cycle component times monitored and analyzed by the assignment engine. For assignment calculations, the optimization system uses a **Design time** configured in the relevant equipment editors (Loader Class, Shovel Class, and Processor Editor) for **Spotting Empty/Loaded**.

2. **Dynamic Spotting Calculation:** The calculated Spotting Time is determined by Jigsaw based on a **rolling average of the last** n **(typically four) trucks loaded at the shovel**.

3. **Loader-Dependent vs. Truck-Dependent Spotting:** Spotting time is measured to determine whether the truck or the loader caused the delay:

• If the time spent spotting is greater than 10 seconds (or a configured threshold) before loading starts, the time is typically declared **Loader Dependent** (the truck waited for the loader).

• If the start of loading is detected within 10 seconds of the truck stopping next to the loader, the spotting time is declared **Truck Dependent** (the loader waited for the truck).

4. **Productivity Metric:** The LP model uses spotting time, along with digging rate and standard truck size, to calculate the **Shovel Capacity** (or LP Dig Rate), which is an essential factor in setting required haulage and determining the efficiency of the shovel. The total shovel tons per hour KPI calculation includes the Total Spot Time. Lower spotting times increase the calculated capacity and haulage requirements, favoring that route for assignments.

Ultimately, the accuracy of recorded spotting data is paramount for maintaining **data integrity**, which ensures that the automated assignment algorithms, such as the Dynamic Programming (DP) model, make optimal decisions regarding truck assignment to minimize non-productive time, such as queuing.

--------------------------------------------------------------------------------

## Mine Fleet Management: Availability and Assignability

The sources define **Availability & Assignability** as paramount concepts in mine fleet management, crucial for effective **Assignment Behaviors and Monitoring** within systems like DISPATCH and Joptimizer. These concepts determine whether machines are ready to perform work and whether the optimization engine is permitted to include them in automatic assignments.

Defining Availability and Assignability

While often linked, availability and assignability refer to distinct operational states:

• **Availability (Physical Status):** This refers to the actual operational condition of a piece of equipment [1], [2]. Equipment is considered available if its status is **Ready**, **Shiftchange**, or **Delay**, but not **Down** or **Standby** [3], [1], [2].

• **Assignability (System Permission):** This refers to the software setting, typically managed via an **'Available for Assignment' checkbox**, which permits the automated assignment engine (Dynamic Assignment/DA layer) to issue new assignments to the equipment [4], [5], [6]. A machine must be marked as assignable to be considered for automatic assignments [7].

The goal of checking assignability and availability is to ensure machines are ready to be utilized by the Assignment engine so that production objectives can be met [6]. The Controller is primarily responsible for performing this check, typically at the start of each shift or after a machine is repaired following a breakdown [8], [7].

Impact of Availability on Assignment Behavior and Monitoring

The operational status and assignability of equipment have a fundamental and cascading impact throughout the system:

1. **Exclusion from Optimization:** When a machine is unavailable (e.g., due to a Down or Standby status), it **will be removed from the production plan** calculation performed by the Linear Programming (LP) layer [7], [9]. Similarly, if a dump location is **not open**, trucks will not be sent to it automatically [6].

2. **Delay Management and Assignment:** The system uses status information, particularly **Delay** statuses, when determining assignments [10], [11]. For example, if a loading tool goes on a delay with a known duration, the assignment generator might still send trucks to it if the estimated travel time to the loading tool is roughly equal to or less than the remaining delay time, preventing the loader from having to wait for trucks when it comes off delay [11], [12]. However, trucks on a _non-assignment delay_ are still factored into metrics presented on the Trucking Indication (TI) Page, even though they are temporarily excluded from the assignment engine's calculation [13], [14].

3. **Assignment Failure and Monitoring:** Inaccurate availability data severely compromises the system's ability to make accurate assignments [15], [16], [17], resulting in **poor assignments, queuing, idle loaders, and misdirected loads**, ultimately leading to higher costs per ton [15], [16], [17]. The **Fleet Update Assistant (FUA)** is the main interface for Controllers to review the **Available for Assignment** column and directly enable or disable assignment capability for trucks, loading tools, and processors [8], [3].

Specific Controls for Assignability

Mine Controllers manage assignability using specific controls, especially for loading tools:

• **Change Machine from Assignable:** The **Loading Tool Assistant** page contains the **Assignable tick box** [18]. Unchecking this box allows the Controller to temporarily stop the flow of trucks for operational reasons, such as when the operator needs to finish loading the current queue before taking a break [19]. Crucially, even when a loading tool is made unassignable this way, it **will remain in the Production Plan** calculated by the LP layer [19], [20].

• **Managing Committed Trucks:** When making a loading tool unassignable, the system provides options to control trucks already en route or in queue:

    ◦ **Reassign Trucks On Route:** Rediverts any empty truck currently travelling toward the newly unassignable loading tool [21].

    ◦ **Reassign Trucks In Queue:** Rediverts any truck already queuing at that loading tool [21].

    ◦ **Make Assignable On Delay Start/End:** Ensures the assignability status is reset to **ON** when a delay is started or stopped on the loading tool [21].

In essence, Assignment Behaviors and Monitoring are deeply intertwined with the accurate reporting and setting of equipment availability and assignability, as these foundational controls dictate the universe of options available to the sophisticated optimization logic.

--------------------------------------------------------------------------------

## Mine Control Availability and Assignability Procedures

The process of **Checking Availability** is a fundamental task for Mine Controllers at the start of a shift or throughout operations to ensure that equipment is ready and configured to receive assignments within the overall optimization strategy of the DISPATCH/Joptimizer system (referred to generally as **Availability & Assignability**), [1], [2].

This operational check ensures that resources are accessible to the assignment engine, thereby maximizing utilization and adhering to the production plan [1], [2].

Key Concepts in Checking Availability

Availability and assignability are distinct but related concepts verified by the Mine Controller:

1. **Availability:** This primarily refers to whether the machine's physical operating status is one that allows it to be used for work. Machines are eligible for assignments if their status is **Ready**, **Shiftchange**, or **Delay**, but not **Down** or **Standby**, [3], [4], [5].

2. **Assignability:** This is a specific configuration setting within the software (the 'Assignable' checkbox) that permits the assignment engine (Dynamic Assignment/DA layer) to issue assignments to that machine [6], [1], [2], [7]. A machine must be marked as available for assignment to be considered by the assignment engine [1], [2].

When a machine is deliberately made **unavailable for assignment**, it will be removed from the production plan calculations used by the Linear Programming (LP) layer [2], [8].

Procedures and Tools for Checking Availability

The primary responsibility for checking and maintaining equipment availability and assignability lies with the **Controller** [9], [10], [11].

1. Checking Availability and Assignability of Haul Trucks and Auxiliary Equipment:

The simplest place to review the availability for all machines is the **Fleet Update Assistant (FUA)** [9].

• **Action in FUA:** Controllers review the **Available for Assignment** column for each machine type (Truck, Shovel, Processor, Auxiliary Equipment) [9], [12].

• **Enabling/Disabling:** To enable a machine for automatic assignments (make it assignable), the Controller must **tick the corresponding box** in the `Avail for Assign` column [9], [12]. Conversely, unchecking the box removes the machine from the assignment engine [12].

• **Timing:** This check is typically performed **at the start of each shift** and whenever machines are needed after experiencing a breakdown [1], [2].

2. Checking Assignability of Loading Tools (Shovels) and Processors (Dumps/Crushers):

Loading tools and processors also have a specific assignability setting:

• **Loading Tool Assistant:** Controllers use this page to monitor and adjust the loading tool's configuration [13]. Specifically, they must ensure the **Assignable tick box** on the General tab is selected if the loading tool is intended to receive trucks [7], [14].

• **Processor Assistant:** Processors (crushers, dumps) must also be checked. The **Available for Assignment** checkbox on the Processor Assistant page needs to be selected to make the processor available to the assignment algorithm [15].

If a machine is unavailable for assignment due to a **Delay**, the assignment engine needs accurate data regarding that delay. The system generally knows which equipment is ready to begin the shift based on operators logging on and setting their equipment status to **Ready** [16], [17]. However, the Controller must manage machines on delay, as mismanaged delays (e.g., a non-production activity without an updated status) can lead to inaccurate assumptions about truck requirements [18].

Consequences of Incorrect Availability

If a machine is incorrectly marked as unavailable (or if essential locations like dumps are not "open"), the system cannot generate optimal assignments, leading to inefficiencies:

• **Failed Assignments:** Trucks may be sent **poor assignments**, resulting in trucks queuing, loaders sitting idle, and **misdirected loads** [19].

• **LP Solution Impact:** If a machine is unavailable for assignment, it **will be removed from the production plan** calculated by the LP solution, distorting the optimization base [2], [20].

• **Disruption:** The entire dynamic dispatching scheme can be negatively affected if a supposedly available truck or shovel is late due to inaccurate status information [21].

For instance, at shift start, Controllers must ensure that equipment not scheduled for the upcoming shift is intentionally placed on **Standby** status so that DISPATCH does not consider it in its assignment calculations [22], [23]. Conversely, they must ensure all required shovels and processors are marked as **Ready** and **Assignable** [24], [25].

--------------------------------------------------------------------------------

## Controlling Loading Tool Assignability in Mine Optimization

The action of changing a machine from being **Assignable** (typically by unchecking the 'assignable checkbox') is a direct operational control exercised by the Mine Controller to temporarily halt the automatic assignment of trucks to that specific loading tool. This action is crucial within the broader context of maintaining **Availability & Assignability** in the optimization system.

Purpose and Impact on Assignment Logic

The primary **purpose** of rendering a loading tool unassignable is to **stop the flow of trucks for operational reasons**, even though the equipment might technically still be ready for production [1].

When the `Assignable` checkbox is unchecked in the **Loading Tool Assistant** (or Load Tool Editor):

1. **Halting New Assignments:** The machine remains in the **Production Plan** (meaning its long-term existence and constraints are still recognized by the Linear Programming layer), but the Dynamic Assignment (DA) layer of the system immediately **stops sending new assignments** to that loading tool [1, 2].

2. **Operational Scenarios:** This control is useful in scenarios where the loading tool operator needs time to finish loading trucks already queued before taking a break (such as lunch), or if the loading tool needs to switch its operational mode (e.g., transitioning from `Prime` mode—loading trucks—to `Auxiliary` mode—non-truck related activities like cleanup or batters) [1, 3].

Managing Trucks Already Committed

When a loading tool is made unassignable, the system provides additional options to manage the trucks already assigned to that location:

• **Reassign Trucks On Route:** Selecting this option ensures that any trucks currently **traveling toward the loading tool (empty)** are immediately **rediverted** to a different location [4].

• **Reassign Trucks In Queue:** This option allows the immediate **redirection of trucks already queuing** at the loading tool [4].

These reassignment options are vital for maintaining **fleet utilization** and minimizing non-productive time, preventing vehicles from unnecessary queuing or driving long distances to a destination that has temporarily ceased receiving trucks [4].

Context within Availability and Assignability

The ability to control assignability is critical because a machine must be both **Available** and **Assignable** to be utilized effectively by the optimization engine:

• **Availability vs. Assignability:** A machine might be reported as technically available (Ready, not Down or Standby) but made unassignable by the Controller [5, 6]. The Fleet Update Assistant and the Loading Tool Assistant pages are the main interfaces for reviewing and adjusting a machine's assignability status [7, 8].

• **LP Solution Impact:** Even when a loading tool is made unassignable, the sources indicate that it **will remain in the Production Plan** [1, 2]. However, if a machine becomes unavailable for assignment (either due to being unassigned manually or due to an actual Down status), it **will be removed from the production plan** considered by the Linear Programming solution [6, 9].

• **Delay Integration:** The system offers the option to **"Make Assignable On Delay Start/End,"** which automatically resets the assignability status back to ON when a delay is started or stopped on the loading tool [4].

In summary, changing a machine from assignable via the checkbox gives the Mine Controller granular, real-time control to temporarily manage the flow of the truck fleet, thereby minimizing delays and maximizing efficiency in response to immediate operational needs without needing to formally change the machine's overall status (like placing it on a lengthy Down delay) [1].

--------------------------------------------------------------------------------

## Loading Tool Disablement and Truck Reassignment Control

The sources discuss the options related to reassigning trucks that are currently **On Route** or **In Queue** at a loading tool when that loading tool is deliberately being made unassignable (disabled). These settings are crucial components of the Assignment Behavior controls, which dictate how the Dynamic Assignment (DA) layer of the optimization system manages trucks when a key piece of loading equipment suddenly becomes unavailable.

This functionality is accessed and managed within the **Loading Tool Assistant** page (or Loading Tool Editor) under the **Assignable** checkbox settings.

Purpose and Context of Disabling a Loading Tool

Mine Controllers may need to temporarily stop the flow of trucks to a specific loading tool (shovel/excavator) for operational reasons, such as when the operator needs to finish loading the current queue before going on lunch, or if the shovel is switching operating modes (e.g., transitioning from loading trucks to cleanup work) [1]. This is achieved by unchecking the **Assignable** checkbox for the loading tool [2]. When a loading tool is made unassignable, it remains in the production plan, but the system stops sending new assignments to it [1, 3].

Reassignment Options When Disabling

When the Mine Controller unchecks the "Assignable" checkbox, three related options are available to manage the trucks already committed to that location:

1. **Reassign Trucks On Route:** If this option is selected, the system immediately **rediverts any truck currently traveling toward the loading tool** that is now being made unassignable [2]. This prevents trucks that are still on the haul road (traveling empty) from continuing toward a destination that will not immediately receive them.

2. **Reassign Trucks In Queue:** If this option is selected, the system immediately **rediverts any truck currently queued** at the loading tool [2]. These are trucks that have already arrived at the location but have not yet started loading.

3. **Make Assignable On Delay Start/End:** This option dictates how the assignment status is managed when a delay event is started or ended on the loading tool. Selecting this option ensures that the **assignability status is reset to ON** when a delay is started or stopped on the loading tool [2].

These options provide precise control over existing assignments and mitigate inefficiency that would result from trucks unnecessarily queuing or traveling long distances to a location that cannot currently receive them [1].

Broader Context: Availability and Assignment

The **Availability & Assignability** of machines is central to the optimization process, as equipment must be properly configured to be utilized by the assignment engine [4, 5].

• **Availability:** Refers to whether a machine is physically operational and available for production (e.g., not Down or Standby) [4, 6].

• **Assignability:** Specifically refers to the configuration setting (the checkbox) that allows the Assignment engine to send trucks to that machine [1, 7].

When a loading tool is deemed unassignable, the DA layer adjusts its real-time assignment decisions accordingly. The **"Reassign Trucks On Route/In Queue"** options serve as a fine-tuning mechanism within this assignability control, allowing the Controller to manage the transition smoothly and maintain fleet utilization even when planned or unplanned operational pauses occur at the loading units [2].

--------------------------------------------------------------------------------

## Dynamic Assignment Reassignment Waypoints and Strategy

Reassignment Waypoints (or Reassignment Callpoints) are fundamental to the effectiveness of **Dynamic Assignment (DA)** behavior in the DISPATCH/Joptimizer system, particularly for monitoring and adjusting the movement of trucks when traveling empty to a loading unit [1, 2].

These specialized beacons serve as triggers within the mine's digital map, prompting the system to re-evaluate whether a truck's current assignment remains optimal given real-time operational changes [1, 3].

Function and Trigger Mechanism

A Reassignment Waypoint is a configured function applied to any waypoint within the mine's road network [1]. Its primary role is to **trigger a re-evaluation of the truck's current assignment** upon detection [1]. If the system identifies a more efficient or "better alternative" destination, the office software transmits a new assignment instruction to the truck [1].

The assignment re-evaluation is specifically triggered when a truck enters the road segment that ends with the reassignment callpoint [1, 4, 5]. These triggers can be configured to activate based on the truck's operational state [6]:

• **ReAssign when Loaded:** The reassignment check occurs if the truck passes the waypoint while hauling material [6].

• **ReAssign when Empty:** The reassignment check occurs if the truck passes the waypoint while traveling empty to a loading unit [6].

If a waypoint is configured with both options, the reassignment logic is triggered for trucks traveling through the waypoint in either direction [6]. This automatic request sent to the office software is analogous to a manual assignment request triggered by the operator [3].

Context within Assignment Monitoring

Reassignment Waypoints are a tool for ensuring that assignments remain optimal even as conditions change, directly relating to the continuous monitoring process:

1. **Response to Changing Conditions:** Conditions such as shovel delays, slow digging, truck delays, or the availability of new trucks can make a truck's initial assignment non-optimal [7]. The reassignment check addresses these volatile changes by validating whether the truck should proceed on its original path or be redirected [1, 8].

2. **Optimality Check:** Joptimizer evaluates the assignment by calculating the expected travel time to possible destination shovels and anticipating the queue time once the truck arrives [8, 9]. The system calculates the "best shovel" by analyzing the cost associated with each route, favoring the assignment with the lowest cost, thereby eliminating unnecessary waiting and maximizing productivity [10, 11].

3. **Misroute Correction:** The reassignment mechanism is also triggered by **Misroute Detection** [4, 12]. If an operator deviates from the assigned path by a configured distance (e.g., typically 100 meters, which is the default value), a misroute event is generated, signaling the local JAMS of the truck to request a new assignment from the central server [13, 14]. This ensures assignment logic is re-engaged after non-compliance [8, 14, 15].

Placement and Best Practices

The strategic placement of Reassignment Waypoints is crucial to their effectiveness and avoiding counterproductive effects on assignments:

• **Strategic Placement:** They should be positioned at **strategic points** in the mine, specifically before major intersections, congested road sections, or just before a truck enters a load area containing multiple loading units, because these points require a critical decision regarding routing [1, 16, 17].

• **Timing Requirement:** The waypoint must be placed **before the last possible decision point** on a route [16]. Crucially, they **must not be placed directly at or immediately before an intersection**, as the system needs sufficient time to calculate the new optimal assignment and transmit it to the truck operator before the operator misses a turn [16, 18].

• **Network Integrity:** They should **not be placed in areas with bad network communications**. If communication fails, the reassignment cannot be transmitted to the truck, even if calculated in the office [16].

• **Operator Awareness (Best Practice):** Given that the truck’s assignment may spontaneously change upon crossing the waypoint, a **best practice is to set reassignment waypoints to a different color** on the operator's display. This visual differentiation alerts the operator that they are approaching the trigger point and that their assignment "could change" [19].

• **Avoiding Overuse:** Reassignment Waypoints should be used **sparingly**, as overuse can reduce the effectiveness of the optimizer [19, 20].

If the conditions warrant a reassignment, the system ensures the new assignment does not require the truck to travel backwards (backtracking or reversal) from its current position and heading [8, 12, 21]. The continuous check ensures optimal resource allocation by preventing unnecessary queues or wasted travel time [8, 22].

--------------------------------------------------------------------------------

## Reassignment Waypoints: Mining Optimization Triggers

The sources clearly define the primary function of a **Reassignment Waypoint** as a mechanism designed to **trigger a reassignment request to the office** software (DISPATCH/Joptimizer) for a truck traveling along a haul path. This trigger initiates a system-wide evaluation to ensure the truck's current assignment remains optimal given real-time mine conditions.

The Role of Reassignment Waypoints in Triggering Optimization

Reassignment Waypoints are essentially location-based triggers placed strategically within the digital mine model [1], [2]. When a truck's GPS indicates that it has entered the road segment ending with a configured reassignment callpoint, a request is automatically sent to the central office software [1], [3], [4].

The initiation of this request is driven by several dynamic factors and settings:

1. **Re-evaluation of Current Assignment:** The core purpose of the trigger is to force a **re-evaluation of the truck’s current assignment** to determine if a "better alternative is identified by the system" [1], [5]. This re-evaluation ensures efficiency, especially when conditions, such as shovel delays or slowdowns, may have changed since the original assignment was issued [6], [7].

2. **Assignment Layer Involvement:** When the reassignment callpoint trigger is detected, the **Dynamic Assignment (DA) layer** of the optimization system recalculates whether the truck should receive a new assignment [1], [4]. This is similar to a request triggered by the truck operator pressing the "Request Assignment" button on their field panel [3], [4].

3. **Load State Specificity:** A waypoint can be explicitly configured to trigger a reassignment request based on the truck’s operational state, specifically when it is **Loaded or Empty** [8]. If a waypoint is set for both states, reassignments will be triggered for trucks traveling through the waypoint in either direction [8].

Strategic Placement and Design Considerations

The placement of these waypoints is critical to optimizing truck flow and preventing inefficient assignments:

• **Strategic Location:** Reassignment Waypoints should be placed at **strategic points in the mine**, such as just before major intersections or sections of roads that are typically congested [1]. They should also be placed before a truck enters a load area containing multiple loading tools where a key routing decision needs to be made [1], [2].

• **Timing is Key:** The placement should occur **before the last possible decision point on a route** [9]. It is crucial that they are **not placed at or directly before an intersection**, as the system needs adequate time to recalculate the optimal path and transmit the new assignment to the truck before the operator misses a turn [9], [2].

• **Network Considerations:** Waypoints should **not be placed in areas of bad network communications**. If communication is poor, the reassignment may be calculated and issued in the office but will fail to reach the truck [9].

If the re-evaluation identifies a more efficient route or destination, the new assignment is immediately transmitted to the truck from the office software [1]. The system uses the resulting assignment (or lack thereof) to ensure adherence to the overall haulage plan.

Operator Awareness (Best Practice)

Given that the purpose of the trigger is to potentially change the truck's destination mid-route, operator awareness is paramount. A best practice is recommended to mitigate the risk of confusion or misroutes:

• **Color Differentiation:** A key best practice is to set reassignment waypoints to a **different color** on the operator's display screen [10]. This distinct color visually alerts the operator that they are **approaching a waypoint and must be aware that their assignment could change** [10].

--------------------------------------------------------------------------------

## Dynamic Truck Reassignment Based on Load Status

The concept of **Reassignment Waypoints** being triggered specifically when a truck is **Loaded or Empty** is central to how the DISPATCH/MineStar optimization system dynamically manages assignments in real-time. This functionality ensures that the system checks for a more optimal path or destination precisely when the truck's operational state is about to change or has recently changed, thus maximizing efficiency and addressing changing mine conditions.

Triggering Based on Load State

The sources confirm that Reassignment Waypoints can be configured to trigger a re-evaluation of a truck's assignment based on its current load status:

• The system offers checkboxes to configure whether the reassignment trigger should occur **"ReAssign when Loaded"** or **"ReAssign when Empty"** (or both) at a specific waypoint, which are typically found within the **Waypoint Editor** interface [1-3].

• If a waypoint is configured with both options selected ("Reassign when loaded and when empty"), reassignments will be triggered for trucks traveling through that waypoint **in either direction** [1].

This load-state discrimination is crucial because a truck's assignment objective changes drastically depending on whether it is carrying material or returning for a load:

1. **When Empty (Traveling to a Shovel):** When a truck enters the road segment ending with a reassignment callpoint while empty, the trigger fires a re-evaluation of its current assignment [4]. The objective of this re-evaluation is to determine if a **better alternative shovel is available** to load the truck, taking into account factors like shortest travel time, anticipated queue time, and shovel priority [4, 5]. This re-evaluation ensures the truck is directed to the "neediest" shovel or the one resulting in the lowest assignment cost [4, 5].

2. **When Loaded (Traveling to a Dump):** A truck carrying a load (Status = Loaded) may also trigger a reassignment check when passing a relevant waypoint [4, 6]. The objective here is usually to ensure the truck is heading to the most appropriate dump destination, especially considering potential congestion, changing crusher capacities, or blending requirements at the original assigned dump [7, 8].

Context within Optimization and Timing

Reassignment Waypoints are physical markers in the digital mine model (GPS virtual beacons) that trigger the Dynamic Assignment (DA) layer of the optimization system [4, 6].

• **Re-evaluation Logic:** When a trigger is detected, the office software calculates whether a "better alternative is identified" and, if so, a new assignment is sent to the truck [4]. The decision hinges on minimizing cost, balancing synchronization, and fulfilling the overall haulage plan established by the Linear Programming (LP) layer [9-11].

• **Strategic Placement:** Waypoints should be placed at **strategic points** like **just before major intersections** or sections that are typically congested [4, 12]. Placement should also occur where a decision must be made regarding multiple loading tools [4].

• **Best Practices and Operator Awareness:** Due to the risk that an assignment might spontaneously change upon hitting the waypoint, a **best practice is to set reassignment waypoints to a different color** on the operator panel [13]. This visual cue alerts the operator that their assignment _could_ change, allowing them time to notice the new assignment and react before potentially missing a turn [12, 13].

• **Timing Constraints:** The placement should **not be at or directly before an intersection** because the system requires time to recalculate and issue the new assignment to the truck, and the operator needs time to react to the potential change [12, 14].

In summary, configuring Reassignment Waypoints to trigger based on the truck's **Loaded or Empty** state allows the central system to intervene and re-optimize haul routes precisely at critical decision points, maintaining the highest level of efficiency across the continuous haulage cycle [1, 6].

--------------------------------------------------------------------------------

## Visual Best Practice for Dynamic Reassignment Waypoints

The sources explicitly recommend a **best practice** concerning the display of **Reassignment Waypoints** to enhance operator awareness within the DISPATCH (or MineStar/Jigsaw) system.

Best Practice for Operator Awareness

The key best practice regarding Reassignment Waypoints is to set them to a **different color** so that the operator can visually **see when one is approaching and be aware that their assignment could change** [1]. This visual cue is crucial because Reassignment Waypoints function as triggers that initiate a **re-evaluation of the truck’s current assignment** when the truck enters the road segment ending with the waypoint [2-5].

Context of Reassignment Waypoints

Reassignment Waypoints are fundamental components of the assignment and navigation strategy, particularly when conditions in the mine environment are constantly changing [2, 5].

**Function:**These waypoints trigger the dynamic assignment (DA) layer of the optimization system to recalculate the truck’s current assignment and determine if a **better alternative is identified** [2]. If a more efficient route or destination is found (e.g., due to a shovel delay or slowdown), a new assignment is sent to the truck [2, 3, 5-10].

**Placement and Configuration:**

• Reassignment Waypoints should be placed at **strategic points** in the mine, specifically before major intersections or sections of roads that are typically congested [2]. It is also advisable to re-evaluate assignments just before a truck enters a load area containing multiple loading tools [2].

• They should be placed before the **last possible decision point on a route** [11].

• Care must be taken when placing them, as they should **not be placed next to each other on the same route**, nor should they be placed at or directly before an intersection, as the system requires time to recalculate and issue the new assignment, and the truck may miss the turn [1, 11, 12].

• The system can be configured so that any waypoint may be used for reassignment, and the trigger can be set for trucks traveling through the waypoint when **Loaded or Empty** [2, 13].

**Impact on Assignments:**When a truck enters the road segment ending with a Reassignment Callpoint, the dynamic assignment process checks to see if the assignment is still valid [5, 14]. Joptimizer looks at the truck's estimated time of arrival (ETA) and calculates if the existing assignment is still the optimal one to meet haulage requirements [14].

**The Importance of Awareness:**Since the assignment might spontaneously change when an operator passes a Reassignment Waypoint, making the waypoint a distinctive color (the **best practice**) ensures the operator is prepared for a new directive. If an operator is not following the "green line" (Best Path) on their screen, the reassignment request is logged as a misroute exception [15, 16]. Consequently, visual signaling helps reinforce compliance and safe operation.

If a truck operator needs time to notice the reassignment and make the correct turn, particularly before an intersection where a decision must be made, the waypoint should be placed optimally to provide this time [17, 18]. The distinctive coloring supports this critical moment by drawing the operator's attention [1].

--------------------------------------------------------------------------------

## Trucking Indication Page for Assignment Monitoring

The **Trucking Indication (TI) Page** (also known as the Trucking Indicator) is a core component of the DISPATCH/Joptimiser interface, fundamentally serving as a real-time decision support and monitoring tool that validates the ongoing performance of the optimization algorithms [1], [2], [3]. Its primary purpose within the context of **Assignment Behaviors and Monitoring** is to enable Mine Controllers and Supervisors to **optimize truck allocation** to loading tools, thereby improving production outcomes and reducing costs [2].

The TI page summarizes the complex solutions generated by the Linear Programming (LP) layer and monitors how successfully the Dynamic Assignment (DA) layer is adhering to that plan, providing information that updates **with every assignment** [4].

Core Metrics for Assignment Monitoring

The efficacy of assignment behaviors is continuously monitored through key metrics displayed on the TI Page, usually presented for each specific **Production Arc** (the unique combination of the loading tool, processor, and material) [5].

|   |   |
|---|---|
|Metric|Definition & Link to Assignment|
|**Current (Target)**|This reflects the number of trucks **required** to successfully complete the current optimized production plan [6]. This value is the LP layer’s calculated target required haulage necessary to meet production goals [7].|
|**Associated**|This shows the **actual number of trucks assigned** within that specific production arc at that moment (including those en route, loading, dumping, or queuing) [8]. This metric is synonymous with "Current Haulage" used in LP/DP comparison [7].|
|**Computed Max**|This is the theoretical maximum number of trucks the system can effectively handle before production yields cease or increase significantly (i.e., when queuing becomes excessive) [4]. Allocating trucks above this metric increases queue time [4].|
|**Coverage**|Calculated based on the nominal payload of **Associated** trucks relative to the arc's production capacity [9], [5]. This percentage immediately visualizes the operational balance and drives dispatcher decisions [2].|

Direct Influence on Assignment Behaviors

The visual representation on the TI Page directly guides the critical real-time decisions made by the Mine Controller regarding truck flow:

1. **Identifying Imbalance:** The centerpiece of the TI page's monitoring capability is the comparison between the **Current Target** (Required Haulage) and the **Associated** truck count (Current Haulage). This ratio dictates the **Coverage** percentage [9].

2. **Color Key and Action:** The TI Page utilizes a **Color Key** to translate this numerical output into immediate operational status: **Coverage values greater than 100%** (Over-trucked/typically Red) indicate that trucks should be reallocated or parked, as too many resources are dedicated to that arc [9], [2]. **Values smaller than 100%** (Under-trucked/typically Blue) indicate that additional trucks are needed to meet the production goals [9].

3. **Optimization and LP Output:** The TI page serves to prove or disprove the effectiveness of the LP solution. If the Current Target value is smaller than the Computed Max, it suggests an **opportunity to use additional trucks to increase production** if the underlying production constraints are altered [6].

Relationship to the Dynamic Assignment (DA) Layer

The TI Page provides the data foundation that underpins the Dynamic Assignment (DA) logic:

• **Neediest Shovel Determination:** The DA layer's core function is to generate the optimal next assignment by identifying the **"neediest shovel."** This need is evaluated by calculating the difference (deficit) between the **Required Haulage** (derived from LP parameters and visible as the Current Target) and the **Current Haulage** (derived from the Assigned/Associated trucks) [7], [10]. Shovels showing low coverage on the TI page are indicative of high need in the DA calculation.

• **Delays and Assignment Inclusion:** For assignment purposes, trucks on a _non-assignment delay_ are still counted and factored into the TI page's metrics, even though they are temporarily removed from the assignment engine's immediate calculus [3], [11].

Data Integrity and Accuracy

For the TI Page to be an effective tool for monitoring and directing assignment behavior, the underlying mine model data must be accurate, as optimization relies entirely on reliable inputs [12], [13]. The sources warn that the TI indications can be **inaccurate** due to various system flaws [14]:

• **Mine Model Defects:** Issues like **incorrect haul distances** or incorrect assumptions about **dynamic load times** prevent the system from accurately modeling cycle times, which subsequently corrupts the LP calculation and distorts the **Current Target** (required trucks) [14], [15].

• **Operational Status Misrepresentation:** If a loader is performing non-production tasks (like cleanup) but the operator **has not changed the operating mode or applied a delay**, the system may erroneously continue to count the shovel as fully productive, causing the TI page to inaccurately demand more trucks [14].

• **System Timing:** The metrics can become skewed or misleading temporarily, particularly around **shift boundaries**, as trucks transition status while loading units remain technically active [14].

In essence, the TI Page provides a critical, graphical summary of the complex mathematical models governing truck flow, giving the Mine Controller the necessary visualization (often through the color key) to intervene manually or adjust high-level parameters (like priorities or coverage percentage) to maintain alignment with production goals and minimize non-productive time, such as queuing, thereby mitigating higher costs per ton [2], [12].

--------------------------------------------------------------------------------

## Trucking Indication Page: Optimization and Cost Reduction

The sources emphasize that the **Trucking Indication (TI) Page** is a crucial interface tool designed explicitly for the purpose of helping mine personnel, particularly Controllers and Supervisors, **optimize truck allocation to improve production outcomes and reduce costs** in the mining operation.

Core Purpose of the TI Page

The TI Page provides real-time operational data, allowing users to move beyond simple monitoring and actively manage haulage efficiency:

1. **Optimization of Truck Allocation:** The primary function of the TI Page is to present statistical information that enables the Controller to accurately determine the supply and demand for haulage resources across different production arcs (the unique combination of loading tool, processor, and material) in the mine. This information allows for optimizing truck allocation to loading tools to **improve production outcome** [1].

2. **Cost Reduction:** By ensuring that trucks are assigned optimally, the TI Page contributes directly to **reducing costs per ton** [1], [2]. The ultimate objective of the entire assignment engine logic is to meet the production plan with the least amount of truck cycles possible, thereby minimizing the cost of production [3]. Poor assignments (resulting from inaccurate data) can lead to queuing, idle loaders, and misdirected loads, which, in turn, result in **higher costs per ton** [4], [5]. The TI page provides the necessary data to prevent these inefficiencies.

How the TI Page Achieves its Purpose

The TI Page achieves this purpose by presenting comparative metrics derived from the complex LP (Linear Programming) layer of the Joptimiser/DISPATCH system:

• **Determining Resource Balance:** The page displays key metrics for each production arc:

    ◦ **Computed Max:** This indicates the maximum number of trucks beyond which little or no additional production would be expected, helping to **prevent over-trucking** [6]. Allocating trucks above this metric increases queue time [6].

    ◦ **Current Target (Trucks Required):** This metric reflects the optimal number of trucks necessary to complete the current optimized production plan [7]. This value sets the standard against which actual performance is measured [7].

    ◦ **Associated:** This shows the actual number of trucks currently assigned within that production arc (either traveling empty to the shovel or hauling loaded to the processor) [8].

    ◦ **Coverage:** This percentage, calculated by comparing the Associated trucks to the Current Target, determines whether a shovel is **over-trucked, under-trucked, or adequately trucked** [9], [8].

• **Facilitating Decision Making:** Based on these comparisons, the Controller can make informed decisions [1]:

    ◦ If a location is **over-trucked** (Coverage $> 100%$), trucks should be **reallocated** to other loading tools or potentially **parked up** [1], [9].

    ◦ If a location is **under-trucked** (Coverage $< 100%$), **additional trucks should be allocated** to that haul route [9].

    ◦ If the Current Target is lower than the Computed Max, it signals an **opportunity to use additional trucks to increase production** if operational goals are raised [7].

• **Validation of Assignment Logic:** The TI Page also serves as a method of validating whether enough trucks have been allocated to an assignment group, or whether excess capacity is cycling within the group [1]. This ensures the underlying LP assignments are functioning correctly according to the configured mine plan [9].

By synthesizing optimization results and presenting them visually (often using the **Color Key** to indicate over-, under-, or adequately trucked status [10]), the TI Page empowers dispatch personnel to take real-time corrective actions, thereby maximizing machine utilization and production outcomes while maintaining cost effectiveness [1], [11].

--------------------------------------------------------------------------------

## Computed Max in Mine Haulage Optimization

The **Computed Max** is a fundamental metric displayed on the **Trucking Indication (TI) Page** (or Trucking Indicator page) that provides the theoretical upper limit on the haulage resources required for a specific production path within the mine [1, 2]. It is designed to inform the Mine Controller about the point at which increasing the number of trucks servicing a particular area will lead to diminishing returns in production, specifically resulting in increased queue time at the loading tool [1].

Role and Definition of Computed Max

The Computed Max indicates the **number of trucks beyond which little or no additional production would be expected** [1]. It is a critical component of the optimization and decision support modules within the DISPATCH/Joptimizer system, helping to ensure efficient allocation of the truck fleet [1, 3, 4].

The computed maximum value is calculated by the system using a composite analysis that determines the most restrictive bottleneck along the production arc (the unique combination of the loading tool, processor, and material) [2, 5]:

The Computed Max is the **minimum value of three distinct capacity calculations**:

1. **Computed Max of the Shovel (Loading Tool):** This calculation considers the loading capacity of the shovel, taking into account factors like the shovel's type (single versus double-sided), average loading times, and spotting times [2].

2. **Computed Max of the Processor (Dump/Crusher):** This considers the dumping capacity of the destination point, including the maximum unloadable trucks, appropriate spotting times, and dump times [2, 6].

3. **Computed Max of the Available Trucks (Haulage Circuit):** This calculation assesses the capacity imposed by the haulage cycle itself. It accounts for the complete travel time of trucks available to service the given arc, divided by the sum of all non-travel cycle times (load spotting time, loading time, dump spotting time, and dumping time) [2].

The lowest result among these three constraints dictates the Computed Max for that production arc, acting as the system's calculated **maximum theoretical throughput** in terms of average-sized trucks [2]. For example, if a shovel needs 10 trucks to operate at full capacity, but the downstream crusher (processor) can only handle the output generated by 2 trucks, the Computed Max for that production arc will be 2 trucks, reflecting the crusher bottleneck [2].

Context on the Trucking Indication Page

On the TI Page, the Computed Max is used in conjunction with the **Current Target** and **Associated** metrics to gauge operational efficiency [7, 8].

• **Compared to Current Target:** The **Current Target** reflects the number of trucks required to achieve the _optimized production plan_ (which may be a goal below the maximum physical capacity) [7]. If the Current Target value is smaller than the Computed Max, it suggests that there is an **opportunity to use additional trucks to increase production**, provided that the underlying production goals (constraints) are raised [7].

• **Preventing Over-trucking:** If the number of **Associated** trucks (those currently assigned to the arc) approaches or exceeds the Computed Max, the system indicates that the area is likely **over-trucked** (often signaled by the Color Key), confirming that additional resources allocated to this circuit would only lead to non-productive delays and queuing [1, 9].

The goal of Joptimizer is to maximize the utilization of active equipment while conforming to constraints and maximizing production [10]. The Computed Max serves as a key informational piece that helps Controllers guide their management decisions, ensuring they do not allocate excessive resources that unnecessarily increase queue time at the loading tool [1, 4].

--------------------------------------------------------------------------------

## Optimizing Trucking: The Current Target Metric

The **Current Target**, often labeled as "Current(Target)", is a key performance metric displayed on the **Trucking Indication (TI) Page** (or Trucking Indicator page) within the DISPATCH/Joptimizer system. It represents the number of trucks required by the system's optimization model to achieve the current shift's production plan targets for a specific production arc (loading tool, processor, and material combination),, [1].

Role of Current Target within the TI Page

The TI Page provides real-time information to Mine Controllers, Supervisors, and Site Champions to help optimize truck allocation to improve production outcomes and reduce costs [2]. The Current Target is the central value derived from the Linear Programming (LP) calculation that reflects the ideal level of haulage resources needed for each operational circuit [1], [2].

**1. Definition and Calculation:**The **Current Target** reflects the number of trucks needed to fulfill the **optimized production plan** [1]. This value is directly related to the shovel's capacity constraints, dumping capacity constraints, blending constraints, and overall haulage capacity constraints, as calculated by the LP algorithm [3], [4], [5].

**2. Relationship to Performance and Capacity:**The Current Target value is crucial because it is directly compared against the **Associated** truck count (the number of trucks currently assigned or en route to the production arc) [6]. This comparison determines the **Coverage percentage** [7].

• If the **Current Target** value is significantly higher than the actual trucks assigned (**Associated**), the circuit is likely **Under-trucked** (often indicated by blue or low percentage coverage), meaning there are not enough trucks to meet the optimized plan [7].

• If the Associated count meets or exceeds the Current Target, the circuit is considered **Adequately Trucked** or potentially **Over-trucked** [7].

**3. Indicators of Opportunity:**Target values that are **smaller than the Computed Max** indicate an opportunity to use additional trucks to increase production [1]. The **Computed Max** represents the theoretical maximum number of trucks beyond which little or no additional production would be expected [8]. If the required volume (Current Target) is below this theoretical maximum, it suggests constraints (like blending requirements or lower production goals) are preventing full use of the physical capacity available, indicating an opportunity to increase the production goal if feasible.

Context within Optimization and Constraints

The value of the Current Target is heavily influenced by the configuration and constraints imposed on the LP layer:

• **Production Plan Input:** The LP layer maximizes productivity while adhering to operational constraints and production goals (or targets) set by the user [9], [10], [11]. The Current Target reflects the truck allocation necessary to meet these defined goals [10], [12], [13].

• **Capacity Constraints:** The LP layer performs calculations to define haulage capacity constraints. The calculation for **Shovel Capacity** (or LP Dig Rate) considers the standard truck size, dig rate, and spot time [14], [15]. This capacity is adjusted by the **Desired Coverage** percentage setting and the Target Dig Rate set for the shovel, resulting in the final capacity requirement that feeds the LP constraints [16], [17]. The Current Target reflects this constrained rate in terms of trucks required.

• **Constraints on Inaccuracy:** The sources highlight that the calculated **Current Target** (or Trucks Required) may be **inaccurate** if underlying data is flawed [18]. This can be due to:

    ◦ **Mine Model Issues:** Such as incorrect haul distances or inaccurate dynamic load times [18].

    ◦ **Operating Modes:** If a loader is performing non-production work (like cleanup) but has not changed its operating mode, the LP calculation may erroneously inflate the truck requirements (Current Target) [18].

    ◦ **Timing:** Data often skews around shift boundaries as equipment transitions statuses, causing temporary inaccuracies in required truck counts [18].

In summary, the **Current Target** on the TI Page is the operational mandate derived from the LP optimization—it tells the dispatcher exactly how many trucks (in terms of average truck size) are theoretically needed on each haulage circuit right now to achieve the established production plan, providing a crucial basis for real-time fleet management decisions [1], [19].

--------------------------------------------------------------------------------

## Trucking Indication and Associated Haulage Metric

The term **"Associated"** refers to a critical metric displayed on the **Trucking Indication (TI) Page** (or Trucking Indicator page in the DISPATCH/Joptimiser system) that represents the haulage resources currently dedicated to a specific production path involving a loading tool or processor. This metric is essential for assessing the operational balance of the fleet.

Definition and Function on the TI Page

The "Associated" column (or metric) on the Trucking Indication page specifically displays the **number of trucks currently assigned within the production arc** [1].

This count includes trucks that are:

• **On route to the shovel** from the destination (empty path).

• **Assigned to the processor/dump** from the shovel (loaded path).

• Currently **in the process of loading and dumping** [1].

• **Queuing** at the loading tool or processor [1].

In essence, the associated number represents the real-time allocation of hauling equipment to a specific **Production Arc**, which is defined as the unique combination of the loading tool, processor, and material [2].

Relationship to Coverage and Optimization

The Associated truck count is directly compared against the calculated needs of the operation to determine the effectiveness of the fleet utilization, often visualized using the Color Key system:

1. **Input for Coverage Calculation:** The Associated count is the actual metric used to compute the **Coverage percentage** for that specific production arc [1, 3]. Coverage is calculated based on the nominal payload of the associated trucks relative to the arc’s production capacity [2, 3].

2. **Indicators of Operational Balance:**

• If the Associated number of trucks is sufficient to achieve the production plan, the coverage will be **100% (Adequately Trucked)** [3].

• If the coverage is **greater than 100% for extended periods**, the system indicates an **Over-trucked** condition, suggesting that the Associated truck count is too high, potentially causing unnecessary queuing [3].

• If the coverage is **smaller than 100% for extended periods**, the system indicates an **Under-trucked** condition, meaning the current Associated count is too low to meet the production goals for that arc [3].

Context within the Assignment Process

The Associated trucks metric reflects the active assignments dictated by the Dynamic Assignment (DA) layer, which uses the theoretical flow rates established by the Linear Programming (LP) layer.

The calculation for the Associated metric is critical in determining the **"Neediest Shovel"** [4-6]:

• The **Required Haulage** (LP calculation based on shovel capacity and cycle time) is compared against the **Current Haulage** (represented by the Associated trucks, calculated based on the truck sizes currently assigned to the load node) [5, 7, 8].

• The difference between the required haulage and the current haulage (**Total Haulage**) determines the shovel's need ranking [5, 6, 9, 10]. A higher difference indicates a greater need for trucks, making the shovel the "neediest" [5, 6].

In summary, the **Associated** metric is a real-time count of trucks physically tied to a specific mining production path (loading unit to dumping destination), acting as the central actual value against which the optimization system measures its success and dynamically adjusts truck assignments [1, 4].

--------------------------------------------------------------------------------

## Trucking Indication Page Color Key Diagnostics

The sources describe the **Color Key** as an integral visual aid found at the bottom of the **Trucking Indication (TI) Page** (or Trucking Indicator page in DISPATCH) which immediately communicates the status of truck coverage relative to the required production capacity of the loading units [1], [2].

This color-coding system helps Mine Controllers, supervisors, and site champions rapidly determine the utilization state of the loading tools (shovels/loaders) and the mining circuits [3].

Role of the Color Key

The Color Key serves as a mechanism for interpreting the efficiency metrics displayed on the TI page, specifically relating the calculated required number of trucks to the number actually allocated or associated with a production path [3], [1]. This comparison is often expressed as the **Coverage percentage** [4], [5], [6].

The color key uses three primary indicators:

1. **Over-trucked (Red):** This color indicates a situation where the resources allocated to a loading unit or production arc exceed the necessary capacity, often leading to increased queue time at the loading tool [1], [7].

2. **Adequately Trucked (Green):** This color signifies that the loading tool or production path has the optimal or desired number of trucks necessary to meet its current production capacity or objectives [1].

3. **Under-trucked (Blue/Red/Yellow):** This color signifies that the loading tool or production path is currently operating with fewer trucks than required to meet its targeted production capacity [1], [8].

The visual presentation on the TI page updates with every assignment, ensuring controllers have up-to-date feedback on the operational balance of the fleet [7].

Color Key and Optimization Context

The colors directly reflect the output of the Linear Programming (LP) and Dynamic Programming (DP) optimization layers, which continuously calculate the optimal number of trucks needed (the **Computed Max** or **Current Target**) versus the trucks currently flowing (**Associated** or **Actual Trucks**) [3], [7], [9], [10].

• **Under-trucked conditions** (indicated by the Under-trucked indicator, typically Blue or sometimes reflected by a high yellow/red status on the coverage percentage) are usually associated with **Coverage values smaller than 100% for extended periods** [4], [6]. This means additional trucks should be allocated to that loading tool run to achieve the production plan [4], [11].

• **Over-trucked conditions** (indicated by the Over-trucked indicator, typically Red) are associated with **Coverage values greater than 100% for extended periods** [4]. This indicates that trucks should be reallocated to other loading tools or potentially parked [4], [12], [13].

For example, a shovel may show **100% coverage** (Green/Adequately trucked), meaning it is meeting its production plan in tons per hour, but this is distinct from full saturation, meaning the shovel could still potentially handle more tons if required [4].

Causes of Color Changes (Inaccuracy)

A shift in color on the TI page alerts the controller to action, but the sources also caution that the indications may not be accurate due to underlying data issues:

• **Mine Model Issues:** Incorrect haul distances or dynamic travel times can skew the LP calculations, leading to inaccurate required truck counts, corrupting the color indications [14].

• **Operating Mode Errors:** If a loader performing clean-up activity hasn't changed its operating mode or status to a delay, the system erroneously counts it as fully available, inflating the required truck count and potentially forcing the display to show an unwarranted under-trucked (Blue) state [14].

• **Timing Issues:** Data often becomes skewed at shift boundaries (start and end) as equipment statuses transition (e.g., trucks going on delay while loading tools continue operating), which temporarily affects the accuracy of the displayed coverage, thus causing misleading color indications [14].

• **Shovel Status:** If a shovel is temporarily disabled but still remains in the production plan, it can disrupt the truck balance calculations, potentially leading to incorrect color coding for surrounding circuits [15].

In summary, the Color Key is a highly visible, real-time diagnostic tool within the Trucking Indication page, translating the complex relationship between available haulage resources and programmed production requirements (calculated by LP) into actionable visual alerts of over-, under-, or adequate truck coverage [3], [1], [7].

--------------------------------------------------------------------------------

## Trucking Indication Page Inaccuracy Causes and Resolution

The **Trucking Indication (TI) Page** is a crucial decision support tool that allows users, including Controllers, Supervisors, and Site Champions, to monitor the required quantity of trucks relative to the available loading units and optimize truck allocation to maximize production and reduce costs [1], [2], [3]. The data displayed on the TI page is designed to reflect the optimal allocation calculated by the Linear Programming (LP) layer of the DISPATCH/Joptimizer system [3], [4].

However, the sources explicitly list several common scenarios and configuration issues that cause the information shown on the Trucking Indication page to be **inaccurate** or "not accurate" [5], [6]. When the data is inaccurate, it misrepresents the operational status of the mine, leading to potentially poor assignment decisions [7], [8], [9], [10].

The main reasons the **Trucking Indication is not accurate** fall into three primary categories: Mine Model Issues, Improper Equipment Status/Operating Mode, and Data Latency/Shift Boundaries.

1. Mine Model Issues

The Trucking Indication page relies heavily on the underlying quality and realism of the mine model and its real-time parameters. Flaws in these foundational configurations directly corrupt the resulting LP calculation, thus skewing the TI metrics (like **Computed Max** and **Current Target**) [6], [11].

• **Incorrect Haul Distances/Dynamic Times:** The TI uses values generated from the optimization model (LP) [3], which itself depends on accurate travel times and road distances calculated by the Best Path (BP) module [12], [13]. If there are **incorrect haul distances** or if **dynamic load times** are **not updating** correctly, the LP calculations will be based on faulty expectations, rendering the TI page inaccurate [6]. For instance, inaccurate mine model data can lead to inefficient assignments, queuing, idle time, and increased costs per ton [7], [8], [14], [9], [10], [15].

• **Best Path Corruption:** The Best Path calculation, which determines the most efficient route, relies on locations and roads having correct elevation values [16], [17]. If **callpoint elevations are incorrect**, or if road segments are wrongly categorized (e.g., classifying a slow, steep segment as fast, level road), the resulting travel times used by the LP layer and displayed on the TI page will be corrupt and inaccurate [18], [19], [20].

2. Improper Equipment Status and Operating Modes

The TI page’s accuracy hinges on equipment status correctly reflecting actual operational status, especially concerning shovels/loaders [6].

• **Operating Mode Not Changed:** A frequent cause of inaccuracy is when the loader (shovel) is engaged in non-production work, such as **cleanup work**, but the operator **has not changed the operating mode** or set the machine on a delay [6]. If the shovel is performing auxiliary tasks (`Auxiliary` mode) but the system still sees it as available for loading (`Prime` mode) [21], the TI calculation (which considers it available) will erroneously demand trucks for that unit, leading to inflated "Required" truck counts or misleading coverage percentages [3].

• **New Loading Tool Added (Post-Delay Update):** When a loading tool comes **off delay** (especially a significant delay like maintenance), it may take a **short period of time to update** its new operational parameters and full readiness across the system before the TI page can reflect accurate, reliable metrics [6].

3. Data Latency and Timing Issues

The TI calculation is highly sensitive to the temporal context of the mining operation [22].

• **Shift Boundary Issues:** Data accuracy **may become skewed at the start and end of the shift** [6]. This occurs as trucks are going on delay for shift change or tiedown while other loading tools or shovels continue to operate [6]. This transitional period often leads to temporary inaccuracies in the calculated requirements and actual usage displayed on the TI page.

• **Non-Assignment Delays:** Trucks placed on a **non-assignment delay** will still be **considered on Trucking Indication** [2]. While the truck is not factoring into the assignment engine's immediate calculations, its inclusion in the "Available" metrics on the TI may lead to a misleading interpretation of fleet readiness [2], [23].

If any of these issues persist, the responsible personnel (Controllers/Builders) are advised to **report the issues to Caterpillar Support** [24]. The essential requirement for the system to function correctly is that inputs entered into the system, such as delays, must always be an **accurate representation of the event** [25], [26].

--------------------------------------------------------------------------------

## DISPATCH Cat MineStar System Utilities and Operations

The sources provide a rich and highly detailed perspective on the **System Utilities and Interface (DISPATCH)**, emphasizing their collective role in achieving optimal **Cat MineStar System Fleet / DISPATCH Operations & Assignments**. The interface components are the mechanisms through which mine controllers and builders interact with the underlying complex optimization algorithms (Best Path (BP), Linear Programming (LP), and Dynamic Programming (DP)) to ensure efficiency, data integrity, and compliance with the mine plan.

Core Interface Components

The DISPATCH (or Fleet/Jmineops) interface is modular, consisting of several key components designed for specific tasks:

1. **Control Panel:** This serves as the **primary launchpad** for the essential applications and utilities used by Mine Controllers, offering immediate access to the main operational screens [1-5].

2. **MineView Application:** This is the comprehensive **visual and operational hub**, providing real-time graphical displays called perspectives (such as **Mine Graphics** and **Haul Route**) for monitoring and controlling the haulage operation [3, 6-10]. It also hosts the Navigation Panel, granting access to all system utilities [3, 11, 12].

3. **DISPATCH Master Keypad:** This is a centralized interface providing **quick access and shortcuts** to numerous DISPATCH utilities, including operational, configuration, and database maintenance tools [1, 13-16].

4. **FormView Window:** This window serves as a host or container for utilities launched via the Master Keypad or directly invoked by typing the utility's specific form name (e.g., `dispatch` or `shvlneed`) [1, 2, 17-21].

5. **Exceptions Window (Inbox):** A critical **real-time diagnostic screen** that posts messages alerting the controller to unexpected events (exceptions) that require **immediate resolution** (Accept or Reject) to prevent disruption of the haulage cycle, ensuring continuous monitoring is vital [1, 22-32].

6. **Report Manager (Reporting Services Portal):** This web-based tool (often utilizing Microsoft Reporting Services) manages and generates historical and real-time performance reports (e.g., equipment summaries, shift production, KPI dashboards) based on data stored in the SQL database [1, 25, 33-39].

Key System Utilities for Operations and Assignments

The core functionality that drives operations and manages assignments is encapsulated in numerous specialized utilities, many of which focus on data integrity, operational control, and optimization configuration:

A. Fleet Control and Operational Management:

• **Dispatch Utility:** Used for **broad dispatching control**, enabling dispatchers to display and change status, location, and destination data for trucks, shovels, and locations, and manually generate assignments or remotely trigger field computer actions (e.g., **Assign, Arrive, Full**) for operators experiencing hardware issues [40-47].

• **Reasons Utility:** Essential for managing downtime records, this tool displays and allows creation/modification of **reason codes** that explain equipment status changes (Down, Ready, Delay, Standby), defining parameters like maximum duration (`Delay Min`) or auto-acceptance (`Auto`) of exceptions [24, 48-58].

• **Truck Utility/Shovel Utility/Aux Utility/Location Utility:** These management tools allow users to modify comprehensive equipment and location data, including operational statuses, locations, maintenance schedules, and assignment restrictions (locks/bars) [59-70].

B. Optimization and Constraint Management:

• **Linear Programming Configuration Utility (LP Config):** Used to configure the fundamental parameters and constraints of the LP model, which dictates the theoretical optimum haulage flow (paths and feed rates) used by the DP model [71-77]. Settings here include shovel priorities, production capacities, and global rules like `LPMATCHTRUCKS` [72, 78-83].

• **Shovel Need Utility:** Calculates the **number of trucks required** by the entire fleet or individual shovels, drawing from the LP solution, aiding dispatchers in parking surplus trucks or adding needed resources [84-87].

• **Shovel Priority/Coverage Utility:** Directly adjusts shovel priority levels (1-6 or 1-10) and coverage factors (% utilization) to prioritize haulage resource allocation in **undertrucked situations**, thereby influencing the LP solution to match production goals [84, 88-94].

• **Assignment Logic and Locks/Bars:** Assignments are highly influenced by constraints (locks/bars) and priorities, often configured via utilities like the Truck or Shovel Utility. Locks impose strict limitations (e.g., truck permanently locked to a shovel), while bars prevent assignments (e.g., barring large trucks from small shovels). Excessive use of these constraints can negatively impact optimization and productivity [95-105].

C. Data Integrity and Cycle Management:

• **Cycle Assistant (Shift Edit Utility):** Crucial for ensuring **production data integrity** by allowing controllers to review and correct historical activity cycles (truck, shovel, auxiliary, drill) to fix errors like missing information, inconsistent data, or abnormal cycle times. It allows splitting or merging incorrect cycle records [84, 106-117].

• **Onboard File Assistant:** Used to generate, upload, and monitor the status of data files (**MWF files**) containing critical configuration information (mine model, equipment settings, operator lists, delays, safety checklists) to the mobile equipment units, ensuring the field computers operate with up-to-date parameters [118-123].

• **Fuel, Lube, and SMU Assistant / Fuel Assistant:** Enables the entry and monitoring of fuel levels (fuel remaining, low/critical thresholds), lube, and Service Meter Unit (SMU) records, crucial for calculating burn rates and generating automatic refueling assignments to minimize downtime [124-133].

In the overarching context of operations and assignments, these components and utilities are continually engaged in a cycle of receiving real-time inputs (GPS, payloads, operator actions), processing them through the BP, LP, and DP models to calculate the next optimum assignment, and validating data integrity to ensure the outputs (truck assignments, shift reports, and KPI metrics) accurately reflect the status and needs of the mine [134-142]. Failures in system data (e.g., incorrect elevations, unchecked restrictions, inaccurate delay statuses) directly undermine the effectiveness of the LP and DP assignments, highlighting the Mine Controller's role as the guardian of operational data quality [143-148].

--------------------------------------------------------------------------------

## DISPATCH Mine Operations User Interface Components

The DISPATCH (or MineStar/IntelliMine/Jmineops) system relies on a set of tightly integrated **User Interface (UI) Components** to allow Mine Controllers and administrators to monitor, control, configure, and troubleshoot the complex, dynamic haulage operation. These components serve as the primary access points for the system's core functionalities and utilities.

The overall DISPATCH user interface environment consists of several major components: the Control Panel, the MineView Application (which contains various perspectives), the FormView Window, the DISPATCH Master Keypad, the Exceptions Window, and the Report Manager.

1. Control Panel

The **Control Panel** is the primary launchpad for the core system applications and utilities [1], [2], [3], [4], [5].

• **Function:** It offers quick access to essential operational screens that a dispatcher needs constantly open or ready for use [3], [6].

• **Key Components Launched:** The Control Panel provides icons to start the **MineView Application**, the **Exceptions Window**, the **DISPATCH Master Keypad**, the **FormView Window**, and the **Packet Switcher Debug Utility** (used for monitoring radio health and connectivity) [1], [7].

2. MineView Application (The Graphical Hub)

The **MineView Application** is the comprehensive visual and operational hub of the system, providing real-time graphical displays of the mine [1], [7], [2], [8], [5], [9].

• **Function:** It allows continuous monitoring and control of the haulage operation through visual representations and includes access to all system utilities via its Navigation Panel [7], [10].

• **Key Perspectives/Components:**

    ◦ **Mine Graphics (or JSMine Map):** Provides a **real-time graphical mine map** that displays the road network, mine locations, and the current positions of equipment [11], [12], [8], [13], [14], [15]. It supports features like **Best Path** calculation (shown as a green line to trucks) [16], [17] and displays movement modes (Current or Historical) [18].

    ◦ **Haul Route (or JSMTC - Jigsaw Mine Traffic Control):** A crucial operational screen used by dispatchers to monitor shovel and truck activity [13], [19]. It uses **haulage graphs** (lines showing flow between shovels and dumps) to visualize assignments [20], shows **queuing** at locations [21], and displays real-time **Transactions** [22]. It also features drag-and-drop functionality for manual reassignments [23].

    ◦ **Dashboards:** Allows users to view graphical representations of Key Performance Indicators (KPIs) (e.g., equipment utilization, delay time) in near real-time, based on data accessed through the SQL Server Reporting Services Portal [11], [24], [5], [25], [26].

    ◦ **Navigation Panel:** This panel, usually visible in MineView, displays all DISPATCH modules (like Time Tracking, Equipment Control, LP Configuration) and their associated utilities for quick launching [27], [10], [28].

3. FormView Window

The **FormView Window** acts as a flexible execution environment and container for running various utilities [1], [29].

• **Function:** It allows users to start any DISPATCH utility by entering its specific **form name** (e.g., `dispatch.frm` or `shovlmod`) into the `Execute Form` field [6], [30], [31].

• **Interface:** It includes a Menu Bar, Tool Bar (with back arrows and refresh), and the Display Area where the requested utility interface appears [30], [32], [33].

4. DISPATCH Master Keypad

The **DISPATCH Master Keypad** is a centralized interface that organizes and provides rapid access to utilities [1], [33], [34].

• **Function:** It is a list of buttons offering quick access to various **DISPATCH Utilities** (like Prestart, Breaks, GPS, Shovel Need, LP Config) and **Database Maintenance Utilities** (like Trucks, Shovels, Reasons, Grades) [33], [34].

• **Access:** It is launched from the Control Panel icon [35]. Utilities launched from the Keypad appear within the hosting FormView window [34].

5. Exceptions Window

The **Exceptions Window** is a mission-critical diagnostic screen requiring immediate controller attention [1], [35].

• **Function:** It posts **real-time messages** ("exceptions") alerting the dispatcher to unexpected or unacceptable conditions in the haul cycle, such as status changes (Down, Delay, Standby), operator errors (e.g., pressing the wrong button), or issues like low fuel/TKPH warnings [36], [37], [38], [35], [39], [40], [41].

• **Intervention:** It requires immediate action, as exceptions requiring input (Accept or Reject) will halt the screen's scrolling until resolved [36], [42], [43]. Accepting exceptions validates the recorded event, while rejecting ignores it [44], [45].

• **Role:** Along with Haul Route/JSMTC and MineGraphics, the Exception Screen is listed as one of the main screens that a dispatcher should always have open [3], [46], [13].

6. Report Manager

The **Report Manager** is primarily the interface for accessing performance and historical reports [1], [47].

• **Function:** It allows users to generate DISPATCH system reports, organized via a folder structure, using Microsoft’s Reporting Services (which interacts with SQL server data) [48], [49].

• **Access:** Reports can be accessed directly via a web browser URL or through the Modular Mining Systems Reporting Portal [49], [50], [51]. Users can configure report parameters (e.g., shift dates) and export data in various formats (XML, CSV, PDF, Excel) [52], [53].

These UI components integrate closely with various specialized **System Utilities** (like Truck Utility, Shovel Utility, Reasons Utility, Delay Monitor, and LP Configuration Utility) that handle specific aspects of equipment management, system configuration, and optimization control.

--------------------------------------------------------------------------------

## DISPATCH System Control Panel Functions and Components

The **Control Panel** is identified as a **main component of the DISPATCH (or IntelliMine NextGen/Jmineops) user interface**, serving as the primary launchpad for key system applications and utilities for Mine Controllers.

Role and Function within the User Interface

The Control Panel acts as the central point from which a user launches the most crucial operational and monitoring components of the DISPATCH system [1], [2], [3], [4].

The overall DISPATCH user interface is composed of several main items: the Control Panel itself, the MineView Application, the FormView Window, the DISPATCH Master Keypad, the Exceptions Window, the Report Manager, and various specialized Utilities [1]. The Control Panel is designed to provide immediate access to the core screens necessary for daily dispatching tasks [1], [5].

Key Components Launched via the Control Panel

The Control Panel contains dedicated icons that allow users to launch the following critical system screens and applications:

1. **MineView Application:** Launched by clicking the MineView icon, this application displays real-time graphical information about the mine’s haulage operation and provides access to perspectives like **Dashboards**, **HaulRoute**, and **MineGraphics**, as well as other embedded utilities [5], [6], [7], [8], [9].

2. **Exceptions Window:** Launched via its dedicated icon (often a caution sign), this window displays messages (exceptions) regarding unplanned events in the haul cycle, some of which require **immediate user interaction (Accept or Reject) to resolve** [2], [10], [11], [12]. The Exceptions screen is considered a main DISPATCH screen that dispatchers should always have open [11].

3. **DISPATCH Master Keypad:** Launched via the Keypad icon, this provides **easy access and shortcuts to numerous DISPATCH utilities** [2], [13], [10], [8].

4. **FormView Window:** Launched via the FormView icon, this allows users to **start a utility by entering the utility name** (form name) [2], [14], [15], [16].

5. **Packet Switcher Debug Utility (PSW Debug Utility):** This tool is also launched via an icon (representing a radio tower) and allows users to **monitor system radio health** and debug communications with individual equipment [5], [8].

Summary of Access and Importance

The Control Panel itself is typically started by clicking the **IntelliMine shortcut icon** on the desktop [5], [6], [3]. By launching these major interface components, the Control Panel provides the dispatcher with the necessary capabilities for effective oversight, configuration, and troubleshooting of the optimized fleet management system [17]. Its primary function is to serve as the **initial interface and application launcher** for all main operational screens and system utilities [1], [2], [14].

--------------------------------------------------------------------------------

## MineView: DISPATCH System Monitoring and Control Hub

The **MineView Application** is a central and indispensable user interface (UI) component in the DISPATCH/Jigsaw system, designed to provide real-time graphical displays, diagnostics, and management tools necessary for monitoring and controlling the haulage operation [1], [2], [3], [4], [5], [6], [7].

Within the larger context of the DISPATCH User Interface Components—which includes the Control Panel, FormView Window, DISPATCH Master Keypad, Exceptions Window, Report Manager, and various specialized Utilities—MineView serves as the comprehensive visual and operational hub [1], [2].

Access and Structure

The MineView application is typically launched by clicking the dedicated **MineView icon** on the **Control Panel** [8], [7]. Upon startup, it initially displays a **Welcome tab**, which lists the available perspectives for the user to select [8].

The application is comprised of several main elements [9]:

1. **Menu Bar and Tool Bar:** Provide access to functions, editing options, and quick links to utilities [9], [10], [11], [12].

2. **Display Area:** The main region where perspectives (like Mine Graphics and Haul Route) are shown in tabs [9], [13]. These tabs can be detached or **"undocked"** from the main MineView application and displayed in separate windows to monitor multiple areas simultaneously [14], [15].

3. **Configuration Panel (Options):** A panel used to select specific items—such as regions, materials, equipment, locations, and statuses—that the user wants to display in the active perspective [9], [16], [17], [18], [19].

4. **Navigation Panel:** Displays **all DISPATCH modules and their associated utilities**, allowing users to launch them while running MineView [9], [20], [21], [22].

Primary Perspectives: Haul Route and Mine Graphics

The two most fundamental perspectives accessed through MineView are **Haul Route** (also referred to as the Jigsaw Mine Traffic Control or JSMTC screen) and **Mine Graphics** (also referred to as JSMine Map) [2], [3], [4], [23], [6], [24], [25].

1. Mine Graphics (JSMine Map)

The **Mine Graphics perspective** provides a **real-time graphical mine map** [2], [26], [27]. This map is the foundation of the DISPATCH system, serving as a **scale representation of all mine locations and roads** that the algorithms use for fleet optimization [28], [29], [30].

Key functions and elements include:

• **Visual Monitoring:** Displays the haul road network, mine locations, and the **real-time positions of trucks, shovels, and auxiliary equipment** [26], [31], [27], [19].

• **Best Path (BP):** Displays the calculated **"best path"**—typically shown as a **green line**—that trucks should follow between two points based on criteria like shortest distance, road status, elevation, and restrictions [32], [33], [34].

• **Icon Customization:** Users can customize the appearance of equipment and location icons, including size and color, to make certain elements more prominent [35], [36], [37].

• **Layer Utility:** This specialized utility, accessed within MineView, controls which layers (e.g., equipment, locations, roads, beacons, vector maps) are shown and determines how objects appear on the display [11], [13], [38], [39].

• **Location and Road Management:** Users can directly **create, delete, open, close, and adjust the alignment of roads** on the map, ensuring the road network matches the physical mine conditions [40], [41], [42].

2. Haul Route (JSMTC)

The **Haul Route perspective** focuses on monitoring the efficiency and flow of the haulage fleet, specifically tracking truck assignments in real time [2], [31], [43], [44]. This view is intended for continuous operational oversight [23].

Key information displayed includes:

• **Haulage Graphs:** Shows the path between dumping points and shovels for each region, depicting the loaded path (shovel to dump) and the empty path (dump to shovel) [45], [46]. These graphs can be configured as **Shovel-centric** (shovels in the middle, dumps at ends) or **Dump-centric** (dumps in the middle, shovels at ends) [47], [48].

• **Assignment Monitoring:** Displays the **current path of each truck** on a timeline, graphically indicating its progress and showing the longest empty and longest full haul times [49], [46], [50].

• **Queue Management:** The **Queues section** shows real-time truck queues at shovels, dumps, and service locations [43], [49], [51], [52]. Shovels are displayed in descending priority order [51], [52].

• **Needs Analysis:** Provides a **haulage needs analysis** (region info), indicating the difference between the **LP Trucks (required)**—the optimal number of trucks needed—and the **Actual Trucks** currently hauling in the region [45], [53].

• **Real-time Transactions:** Displays a log of field transactions, calculation messages, and informational messages received from trucks and shovels [20], [54], [55].

• **Drag-and-Drop Assignments:** Allows the Mine Controller to quickly reassign a truck to a different shovel or dump by **"dragging and dropping"** its icon from one circuit line onto another, generating an immediate or delayed assignment [56], [57], [58].

Both perspectives utilize status colors (e.g., Green for Ready, Yellow for Delay, Red for Down) to visually alert the controller to the operational state of equipment and locations [59], [60], [55]. The visual representations in MineView are critical because inaccurate inputs or poor mine model maintenance can lead to inefficient assignments, queuing, idle time, and ultimately higher costs per ton [61], [62].

--------------------------------------------------------------------------------

## The DISPATCH FormView Utility Window

The **FormView Window** is a fundamental user interface (UI) component within the DISPATCH system, serving as a launching platform and container for running various system utilities. It provides an alternative method of accessing system forms and applications outside of the primary graphical interfaces like the MineView Application and the Master Keypad [1, 2].

Function and Accessibility

The FormView Window allows users, particularly dispatchers and administrators, to directly execute system utilities by entering their corresponding form names [2, 3].

Key points regarding the FormView Window's function and access include:

1. **Starting Utilities by Form Name:** The core function of the FormView window is to enable the user to start a utility by typing the utility's specific **form name** into the `Execute Form` field and clicking **Go** [4, 5]. For instance, a user can start the Dispatch Utility by entering `dispatch` or `dispatch.frm`, or the GPS Utility by entering `gps` [3, 6-8].

2. **Launching Method:** The FormView Window is launched directly from the **Control Panel icon** [4, 5]. It is the second button on the Control Panel [9].

3. **Utility Container:** When a utility is launched using this method, it appears and is displayed within the FormView window itself [5, 8]. The utility takes the appearance of a dedicated screen, such as the example showing the **ShovlMod Utility** [8].

4. **Navigation History:** The window maintains a history, and users can move back through previously displayed utilities using the **back arrow** feature [8, 10, 11].

5. **Alternative Access:** Users can also execute new utilities within the FormView Window by pressing **CTRL+F** and entering the form name at the subsequent prompt [12, 13].

Components and Controls

The FormView window consists of several identifiable parts that facilitate user interaction:

• **Menu Bar:** Contains the File, View, Tools, and Help menus [5, 10].

    ◦ The **File menu** includes options such as `RunForm` (to execute the form name entered in the field), `Back` (to navigate to previous utilities), and `Exit` (to close the window) [10].

    ◦ The **View menu** offers navigation commands like `Form Down`, `Form Up`, `Bottom`, `Top`, and `Refresh` [10].

    ◦ The **Tools menu** allows users to set font style and size, although these changes are temporary and revert when FormView restarts or a utility is accessed via the Master Keypad [14].

• **Tool Bar:** Located below the menu bar, it includes icons for navigating back (`Back`), refreshing the current page (`Refreshes the current page`), and executing the form (`Go`) [11, 14].

• **Display Area:** This is the main section where the requested utility (form) is shown [5, 11].

• **Execute Form** **Field:** This is the input field where users enter the required utility form name [8, 14].

FormView in the Context of User Interface Components

The FormView Window plays a role in the overall **DISPATCH User Interface** by providing flexibility in accessing the software's vast library of utilities [1, 5]. Alongside the **Control Panel**, **MineView Application**, **DISPATCH Master Keypad**, **Exceptions Window**, **Report Manager**, and other specialized **Utilities**, FormView ensures that advanced users and dispatch administrators have direct command-line-style access to the full functionality of the system [1].

In contrast to the icon-based shortcuts offered by the Master Keypad, FormView requires precise knowledge of the utility's form name but provides a universal way to launch almost any utility within the system without navigating through hierarchical menus [4, 12]. It functions as a flexible shell capable of hosting specialized screens like the **ShovlMod Utility** [8].

--------------------------------------------------------------------------------

## DISPATCH Exceptions Window: Real-Time Operational Monitoring

The **Exceptions Window** (also referred to as the **Exception Screen** or the **Inbox** in related systems like Jmineops) is a critical, high-priority user interface component within the DISPATCH system, specifically designed to monitor unexpected or exceptional conditions that occur during the haulage cycle [1], [2], [3], [4].

Core Function and Priority

The defining characteristic of the Exceptions Window is the **requirement for immediate resolution** from the Mine Controller [5], [2], [6], [7].

• **Real-time Alerting:** The window posts real-time messages called "exceptions" to notify the controller when unexpected or exceptional conditions occur [2], [8]. It uses a high-pitched alarm to alert the user whenever a new exception appears [9].

• **Intervention Requirement:** Some exceptions require user interaction (Accept or Reject) to resolve, while others are purely informational [10], [2], [6]. Exceptions requiring input will halt the scrolling of the window until they are addressed [6], [11].

• **Operational Impact:** Controllers must handle these events **as soon as possible** to prevent disruption of haulage cycles, as equipment operators and the central computer wait for input [5], [6]. Once an exception is handled, the information is posted in abbreviated form to the system's transaction screen [11].

Location and Structure within the User Interface

The Exceptions Window is launched directly from the **Control Panel icon** [10], [12], [13]. It is one of the essential screens that a dispatcher should keep open at all times [2], [13].

The typical layout of the Exceptions Window displays the following elements:

1. **Accept (F3) and Reject (F4) Buttons:** These prominently positioned buttons are used to handle exceptions requiring user intervention [5], [2], [14], [15], [3]. Accepting an exception validates the reported event (e.g., equipment status change) [14], while rejecting an exception causes the system to ignore the input (e.g., rejecting an accidental action button press) [15].

2. **Exception Details:** Messages include the current date and time, the number of pending exceptions (those requiring input), and the start time of the event [9], [3].

3. **Input Fields:** Some exceptions allow the user to add or change information before accepting them, such as entering a reason code, comment, or adjusting the starting time of a status change [16], [17], [18].

Types of Exceptions Managed

The exceptions managed by this component primarily relate to data integrity, operational faults, and deviations from the optimized plan:

• **Equipment Status Exceptions:** These appear whenever an operator manually changes a status to **Down, Delay,** or **Standby**, or changes status to **Ready** after being Down or Standby [19], [20], [21]. Accepting these exceptions registers the new status in the system [22]. If a Delay reason code is set to **Auto**, the system automatically accepts the exception [22], [23].

• **Operator Login Exceptions:** These occur if an operator uses an incorrect ID, is not lined up on the equipment, or attempts to log on without the correct qualifications [5], [24], [25].

• **Operator Error Exceptions (Haul Cycle Disruptions):** These include accidental presses of haul cycle buttons (like pressing **Arrive** too early or **Load** when loading has not started), or a truck attempting to load at an inactive shovel [26], [27].

    ◦ For example, if a truck presses **Arrive** when it should finish loading, the controller must accept or reject the **Arrive Exception** [26], [28].

    ◦ Accepting an **Assign exception** always assigns the truck to a loading unit, even if it is incorrect [28].

• **Assignment & Logic Exceptions:** These include misroute messages (when a truck leaves its assigned path) [29], notifications that trucks are late to arrive [30], [31], situations where trucks are unable to determine a valid assignment [32], or when a shovel is unable to be covered (unused shovel) [33], [34], [35], [36].

• **Informational Exceptions:** These provide real-time updates that do not require explicit acceptance, such as notifications about LP calculation results (showing actual versus required trucks) [37], crusher production throughput [38], and equipment communication failures [24].

Controllers are also responsible for correcting historical data that initially appeared as exceptions or contained errors, often by using the Delay Assistant or Cycle Editor utilities to retrospectively edit the status records [39].

--------------------------------------------------------------------------------

## DISPATCH Master Keypad: Interface and Utility Access

The **DISPATCH Master Keypad** is identified as a critical component of the DISPATCH (or MineStar/Jmineops) user interface, functioning as a centralized launchpad that provides Mine Controllers and other users **quick and easy access to the extensive suite of system utilities** [1-3].

The Role of the Master Keypad within the User Interface

The DISPATCH user interface relies on several main components, including the Control Panel, the MineView Application (which contains perspectives like Haul Route and Mine Graphics), the Exceptions Window, and various Utilities [4]. The Master Keypad acts as a direct pathway to launch many of these individual utilities, circumventing the hierarchical menu structures often found within the MineView Application [5, 6].

**1. Access and Location:**The Master Keypad is launched directly from the **Control Panel icon** [1, 3, 7]. Once opened, it presents a matrix of buttons, each corresponding to a specific DISPATCH utility [2].

**2. Core Functionality—Utility Access:**The keypad’s main purpose is to provide **quick access** to the diverse DISPATCH utilities [1, 2]. Users initiate a utility simply by clicking the corresponding button on the keypad [2].

The utilities accessible via the keypad are typically categorized into operational groups, including:

• **Core Dispatching Utilities:** This category includes tools essential for real-time operations, such as Prestart, Breaks, Routes, Shovel Need, Shovel Priority (Shvl Prior), GPS, Maintenance, Blending, Parkup (Tiedown), Load Factors, LP Config, and Interfaces [2, 8-11].

• **Database Maintenance Utilities:** This bottom section of the keypad provides access to tools used to manage foundational data, such as Trucks, Shovels, Auxiliary Equipment (AuxEqmt), Workers, Locations, Reasons, Grades, and Shift Edit [2, 9, 10].

**3. Interaction with Other Components:**When a utility is launched from the Master Keypad, it appears over the keypad itself, often within a hosting frame called the **FormView window** [2, 12]. If a user navigates away from the currently displayed utility, they can use the "back arrow" functionality within the FormView window to cycle through previously displayed utilities [2, 13].

**4. Efficiency in Usage:**The existence of the Master Keypad facilitates efficiency, particularly for tasks that require immediate attention or routine checks. For instance, the keypad provides direct access to tools like the **Reasons Utility** (`reasnmod`), used to manage the codes for Down, Ready, Standby, or Delay statuses, and the **Shovel Need Utility** (`shvlneed`), used to instantly check truck requirements [2, 9, 10].

In summary, the DISPATCH Master Keypad is a key feature of the user interface that serves as a single, accessible window containing shortcuts to nearly all operational and configuration tools within the system, crucial for effective daily dispatching and system maintenance [1, 2, 5, 6].

--------------------------------------------------------------------------------

## DISPATCH System Report Manager and Reporting Functions

The sources explicitly identify the **Report Manager** as a dedicated component of the overall user interface within the DISPATCH system, designed specifically for generating and accessing production-related reports. Its primary function ties directly into the system's larger mandate of monitoring and analyzing mining efficiency.

Role of Report Manager within the User Interface

The Report Manager is classified as one of the key components of the DISPATCH user interface, alongside the Control Panel, MineView Application, FormView Window, DISPATCH Master Keypad, Exceptions Window, and Utilities [1].

It serves as the **primary portal for accessing DISPATCH system reports**, which utilize data gathered during mining operations [2, 3].

Access and Underlying Technology

The Report Manager is fundamentally a web-based reporting tool powered by Microsoft's Reporting Services, which interacts with the SQL server database [3, 4].

Users can access reports through two main tools, one of which is the Report Manager [3]:

1. **Direct Web Access (Report Manager):** The Report Manager home page is accessed via a web browser using a specific URL (`http://WebServerName/Reports`) [5, 6]. This tool presents reports organized in a **folder structure** [4, 5].

2. **Reporting Portal:** The same reports can be accessed through the Modular Mining Systems Reporting Portal (also web-based), which presents reports via a collapsible menu structure [4, 7].

The Report Manager is also used as the entry point for viewing KPI Dashboards, accessible by clicking the **Dashboard Reports** folder [6, 8-10].

Functionality and Output

The Report Manager is crucial for performance monitoring and data analysis, delivering various reports related to mining production and system metrics [11, 12]:

• **Report Generation:** Users generate reports by navigating the folder structure (e.g., clicking the Auxiliary Reports, Communication, Crew, or Equipment folders) and then clicking the desired report name [5, 13].

• **Report Configuration:** Before viewing, the report presents a configuration page allowing the user to set parameters such as the specific **shift or range of shifts** to include in the report [13, 14].

• **Data Analysis Tools:** Once a report is generated, the interface provides tools for interacting with the data, including finding specific text strings, printing the report, and exporting the report data into various formats such as **XML, CSV, TIFF, PDF, HTML, or Excel** [15, 16].

• **Data Integrity Connection:** The data used in these reports originates from the constantly updated Pit and Shift data tables within the SQL database [17]. The accuracy and completeness of this data are essential for the system to generate accurate production recording and precise real-time Key Performance Indicators (KPIs) [18-20].

In essence, the **Report Manager** translates the raw, complex data stored in the operational database (generated through optimization, communication, and manual logging activities) into structured, consumable reports essential for oversight, analysis, and managing the entire mining enterprise [2, 4, 21].

--------------------------------------------------------------------------------

## DISPATCH Mine Haulage System Key Utilities

The sources and our conversation history detail a wide range of **Key Utilities** that form the operational backbone of the DISPATCH system interface, enabling mine controllers and builders to manage, configure, troubleshoot, and optimize the haulage operation using real-time data and sophisticated algorithms. These utilities are critical components that ensure the accuracy of the mine model and the efficiency of equipment assignments.

The utilities generally fall into categories relating to equipment control, optimization configuration, data management, and diagnostics. They are typically accessed via the **DISPATCH Master Keypad** or the **MineView application**'s Navigation Panel/FormView Window [1-6].

Here is a discussion of the major key utilities mentioned in the sources:

1. Equipment and Operational Status Management Utilities

These utilities primarily manage the real-time availability and condition of the mining fleet, addressing status concerns like **Ready**, **Down**, **Delay**, and **Standby**:

• **Dispatch Utility (or TBT DISPATCH UTILITY):** This is characterized as providing **broad dispatching control** over the mining operation [7, 8]. It is essential for displaying and modifying key information for trucks, shovels, and locations, generating manual truck assignments, and executing actions on behalf of field operators (e.g., triggering status changes like Down, Ready, or Delay remotely) in case of field computer malfunctions [7, 9-11].

• **Reasons Utility (or TBT REASONS UTILITY):** This critical tool is used to display, create, change, and delete **reason codes** that describe the specific causes of equipment status changes (Down, Ready, Standby, and Delay) [12-14]. Its configuration directly impacts system behavior, notably setting the expected maximum duration for delays (`Delay Min`) and specifying which delays automatically bypass dispatcher review (`Auto`) [12, 15, 16].

• **Delay Monitor / Delay Assistant:** These utilities are central to managing temporary downtime. The **Delay Monitor** allows controllers to view, monitor, and manage active and future delays, including the ability to stop, extend, or edit a delay [17, 18]. The **Delay Assistant** is used for filtering and correcting **historical delay records** through actions like splitting, merging, or changing delay details to ensure data accuracy for reporting and operational planning [19-28].

• **Prestart Utility (or Prestart Module / TBT PRESTART ITEM UTILITY):** This utility is integral to safety management. It allows for the creation of **maintenance and safety checklists** that operators must complete upon logging onto equipment [12, 29, 30]. It ensures operational readiness and can be configured to trigger specific actions (Record Item, Contact Dispatcher, Do Not Operate) if an item fails inspection [31-33].

• **Tiedown Utility (or Parkup Utility):** Used for managing end-of-shift logistics, the utility manages automatic or manual assignment of trucks to **tiedown locations** (synonymous with Parkup and Shiftchange) [5, 34-36]. It determines where trucks park for operator changes or standby, aiming to maximize production time before shift end [37, 38].

2. Optimization and Planning Utilities

These tools feed or interpret the outputs of the Linear Programming (LP) and Dynamic Programming (DP) optimization models:

• **Shovel Need Utility (or TBT SHOVEL NEED UTILITY):** This utility is used to calculate and report the **required number of trucks** for the overall fleet and for individual shovels [39-41]. It draws information from the LP solution, factoring in average truck size and queue time [42]. It also features a `Compute LP?` option to force the LP algorithm to calculate a new route selection scheme [43, 44].

• **Linear Programming Configuration Utility (LP Config):** This high-level tool allows system administrators (Builders) to define the fundamental assumptions used by the LP model when creating optimized haulage circuits and feed rates [45-49]. This includes configuring global parameters (like `LPMATCHTRUCKS`), shovel priority factors, and proportional feeding mechanisms that ultimately dictate truck assignment patterns [45, 46, 49].

• **Shovel Priority/Coverage Utility:** Directly linked to LP configuration, this utility manages how LP prioritizes shovels in **undertrucked situations**, using a ranking scheme (e.g., 1-6 or 1-10) and coverage factors to allocate haulage resources preferentially [39, 50, 51].

3. Maintenance and Configuration Utilities

These provide detailed control over underlying data structures:

• **Auxiliary Equipment Utility:** This manages data for non-haulage equipment (drills, dozers, water trucks) by allowing manual recording of their statuses, locations, and operators, particularly crucial for equipment lacking DISPATCH field hardware [52-54].

• **TruckMod Utility / ShovelMod Utility:** These utilities are fundamental **Database Maintenance Utilities** used to add, delete, and modify detailed equipment specifications, including technical and maintenance data (e.g., fuel consumption factors, Max TMPH, shovel digging rates, and status reasons) [2, 39, 55, 56].

• **GPS Utility (or TBT GPS UTILITY):** This specialized tool manages location and tracking integrity. Its functions include viewing and modifying virtual beacon data (ID, type, radius), performing coordinate conversions (Longitude/Latitude, Mine, MMS Grid), and querying the current Easting/Northing coordinates for mobile equipment via radio [7, 57-61].

• **Fuel, Lube, and SMU Assistant:** Used to view and manually create historical fuel or Service Meter Unit (SMU) records, enabling monitoring of fuel levels and maintenance intervals [62, 63]. Although it creates historical fuel records, it generally does not update the current live fuel level of a truck [64-66].

• **Cycle Assistant (for Truck, Loader, Auxiliary, Drill Cycles):** This tool is essential for checking and maintaining the accuracy of **production data cycles** [67-69]. Controllers use it to review detailed activity information (loading, travel, spotting, dumping) and correct errors by splitting or merging cycles, ensuring data integrity for reporting [70-75].

These key utilities are interdependent, collectively processing the vast amount of real-time data flowing through the physical and logical interfaces (like the onboard computers and the central servers) to ensure the optimization models continuously drive efficient haulage operations [76-78].

--------------------------------------------------------------------------------

## DISPATCH System Control Utility: Function and Role

The **Dispatch Utility** (form name: `dispatch` or `dispatch.frm`) is a pivotal, multi-functional tool within the DISPATCH system, primarily offering **broad control** over the mining operation, extensive real-time data display, and the ability to execute **manual assignments** and simulated **field computer actions** on behalf of operators. It is classified as a core component of the system's utilities, particularly those used during shift operation and troubleshooting.

Role and Access within Key Utilities

The Dispatch Utility provides comprehensive access to key equipment and location data, consolidating control functions typically spread across various specialized utilities:

• **Broad Dispatching Control:** The utility provides **broad dispatching control** over the haulage operation [1, 2]. Mine Controllers primarily use it to monitor and adjust critical operational parameters during the shift [2, 3].

• **Accessibility:** It is accessible via the **DISPATCH Master Keypad** [1, 4], the **FormView window** (by entering `dispatch` or `dispatch.frm`) [5, 6], or the `Equipment Control` module within the MineView Application Navigation Panel [7, 8].

Key Functionality: Data Display and Control

The Dispatch Utility window is divided into three main sections—Truck, Shovel, and Location—each displaying and allowing modification of critical real-time data for the selected unit [2, 9-11].

**1. Data Display and Modification:**

• **Truck Section:** Displays essential information for a selected truck, including **Truck Status** (Ready, Down, Delay, Standby), current Location, Destination, Material and Grade being hauled, fuel level (`Fuel Left`), and existing Locks (Shovel, Dump, Region) [9, 12]. The Controller can **change the truck’s location** manually, which is necessary if a truck is moved to a maintenance shop after a breakdown [13, 14].

• **Shovel Section:** Displays real-time data for shovels (including excavators and front-end loaders), such as **Shovel Status**, Digging Rate (Tons/Hr), Location, and current priority settings (Ore/Waste Priority) [10, 15]. Controllers can **change the shovel’s status**, update its material type and grade, or manually adjust its digging rate if the shovel moves to a drastically different mining face (e.g., from blasted material to cleanup) [16-19].

• **Location Section:** Displays crucial operational information for fixed locations like dumps, crushers, and stockpiles, such as **Status**, **Target Feed** (tons/Hr), Dump Capacity, and **Material Types accepted** [11, 20, 21]. Controllers can **change the status of a location** (e.g., set a fuel bay to Down if it is out of order) [20].

**2. Manual Assignments and Scheduled Actions (Schedule Action Field):**

The `Schedule Action` field is a core feature that enables broad control over equipment status and assignments, acting as a direct interface to manipulate the system state [12].

• **Simulating Field Computer Actions:** The utility allows the dispatcher to **remotely press field computer buttons** (or simulate the action) for equipment operators who may be experiencing hardware difficulties [22-24]. Actions available include pressing buttons for status changes (**Down, Ready, Delay, Standby**), **Logon**, and cycle management (**Assign, Arrive, First Bucket, Full, Fuel, Recall**) [24, 25].

    ◦ For example, if a truck arrives at a shovel but the operator's field computer malfunctions, the Controller can press **Arrive** for the operator [23].

    ◦ If a shovel finishes loading, the Controller can send a **Full** action to the system, which triggers the dump assignment for the truck [26, 27].

• **Generating Truck Assignments (****Assign Truck to****):** This function allows the dispatcher to **manually override** existing assignments and send a truck to **any location defined in the database** (shovel, dump, shop, fuel island, etc.) [28, 29].

    ◦ The manual assignment can be executed **immediately** or **deferred** until after the truck completes its current action (e.g., dumping its load), providing scheduling flexibility [30].

Integration with Operational Management

The use of the Dispatch Utility is integral to several crucial dispatcher workflows:

• **Shift Preparation:** It is used extensively at the start of a shift to check and change equipment/location statuses, ensuring required trucks and shovels are **Ready** (and unwanted ones are **Standby**), which is critical for optimization calculations [2, 31, 32].

• **Troubleshooting:** When handling exceptions, particularly Down or Delay statuses, the Dispatch Utility allows the Controller to input missing reason codes and quickly apply corrective status changes, overriding the field unit if necessary [2, 33, 34].

• **Locks and Priorities:** Although specialized utilities exist (like the Shovel Priority/Coverage Utility), the Dispatch Utility provides a quick interface to set shovel priorities or manually lock trucks to shovels, although excessive constraints are cautioned against as they can adversely affect optimization [9, 12, 18, 35].

--------------------------------------------------------------------------------

## DISPATCH Shovel Need Utility and Truck Requirements

The sources identify the **Shovel Need Utility** as a critical diagnostic and operational tool within the DISPATCH system, primarily dedicated to calculating and displaying the current truck requirements for the shovel fleet. This function is essential for dynamically balancing the haulage operation.

Purpose and Core Functionality

The primary purpose of the **Shovel Need Utility** is to determine **how many trucks the shovel fleet currently requires** to operate effectively, both for the overall fleet and for individual shovels [1-3].

This utility provides real-time insights that help dispatchers determine whether to park unnecessary trucks or add more trucks to the fleet to achieve production goals [4, 5]. The utility displays key production data for reference, including the shovel ID, its ore and waste priorities, status, current material type, location, and digging rate (tons per hour) [2].

Access and Interface

The Shovel Need Utility is accessible through the main system interfaces, confirming its role as a key operational utility:

1. **DISPATCH Master Keypad:** The utility is explicitly listed on the Keypad interface under the name **Shvl Need** [6, 7].

2. **FormView Window:** It can be invoked by entering its corresponding form name, which is `shvlneed` [2, 8].

Calculation of Truck Requirements

The utility's core output is the comparison of actual operating trucks versus the required trucks, derived from the Linear Programming (LP) model:

• **Overall Requirements:** The utility displays the **Actual/Required Trucks** data for the entire pit (e.g., "6.0 / 5.0") [3, 4]. This comparison indicates if the mine is overtrucked (more operating trucks than required) or undertrucked (fewer operating trucks than required) [4].

• **LP Inputs for Calculation:** The required truck calculation is based on a mine-specific **average truck size** (e.g., 300 tons) and a **specified queue time** (e.g., 30 seconds) at the shovel [4]. This information is derived from the LP solution, which relies on calculated travel times, spotting times, loading rates, and production capacities [9-11].

• **Individual Shovel Requirements:** To determine the specific number of trucks required by each operating shovel, the user must click the **Print LP Summary Report** option within the Shovel Need Utility [12]. The LP Summary Report then provides detailed information on **Required Trucks at Coverage** for each shovel's production path [12-14].

Integration with Linear Programming (LP)

The Shovel Need Utility is intrinsically linked to the LP algorithm, which forms the core of the optimization strategy [15]:

• **LP Summary Report:** The utility provides direct access to the **LP Summary Report**, which details paths, tons/hour, and required trucks as calculated by the LP solution [12, 16]. This report also displays the **Actual/LP Used Trucks by Pit** [17, 18].

• **Forcing Recalculation:** The utility includes a **Compute LP?** option, allowing the dispatcher to force the LP algorithm to calculate a new route selection scheme (LP solution) [2, 19]. This is often necessary when production output lags or after making changes to LP restrictions [19, 20].

The accuracy of the truck requirement figures helps the Controller manage the fleet optimally, ensuring resources are directed where they are most productive [21, 22]. For instance, the calculation of required trucks helps confirm if a pit is **overtrucked**, indicated when the LP coverage for all shovels is 100%, suggesting excess haulage resources [23].

--------------------------------------------------------------------------------

## DISPATCH Reasons Utility: Status Code Management

The **Reasons Utility** is a dedicated administrative tool within the DISPATCH system used to **display, create, renumber, change, and delete reason codes** associated with changes in equipment status [1, 2]. This utility is foundational to data integrity and reporting, particularly concerning non-productive time for operational equipment.

Function and Purpose of Reason Codes

A reason code is a numerical identifier linked to a description, used to explain the specific cause for any change in equipment status (Down, Ready, Standby, or Delay) [1, 3].

When an equipment operator triggers a status change on a field computer (e.g., pressing "Down"), they also enter a reason code [3, 4]. If the operator fails to enter this code, the Controller must input it manually through the Exceptions window to ensure accurate reporting [5, 6]. The core purpose of capturing this data is twofold:

1. **System Reporting:** Accurate reason codes ensure comprehensive historical reporting [6, 7]. Reports would be incomplete or inaccurate if a reason code is missing or incorrect (e.g., reports would show Down for "UNKNOWN" reason if the code was not entered) [8, 9].

2. **Dispatch Control:** The configuration of the reason code dictates how the system and the optimization engine (LP/DP) react to the status change [10, 11].

Reasons Utility as a Key Utility

The Reasons Utility (`reasnmod` form name) is consistently identified as a core component of the Dispatch system's maintenance and control functions [1, 2, 12].

**1. Configuration and Editing:**The utility is categorized under **Database Maintenance Utilities** on the **DISPATCH Master Keypad** [13]. It allows system administrators or authorized users to manage the following settings for each status reason code:

• **Code and Description:** Defines the numerical code and its associated description (e.g., code 0105 for "LUNCH" Delay) [14, 15].

• **Category:** Assigns a general type to the reason code (e.g., Planned Maint, Standby) [16].

• **Auto-Acceptance:** For **Delay** reason codes only, the `AUTO` field can be set to "YES" to automatically accept the resultant exception on the Exceptions window, removing the need for manual dispatcher approval [15, 17, 18].

• **Delay Duration (****Delay Min****):** For Delay status reason codes, this sets the maximum expected length of the delay (in minutes). The Dispatcher must monitor actual delay durations against this defined limit; if the delay exceeds this time, the Controller may need to manually change the equipment status to Down or Standby to remove it from optimization planning [10, 15, 19, 20].

• **Maintenance Time (****Maint Time****):** If set to "Yes," the system logs time spent in the status/reason code combination against the working time since the last preventive maintenance [15].

• **Equipment Application:** The utility allows selecting which equipment types (Truck, Shovel, Aux Eqmt, Location/Crushers) the reason code menu should be available to on the field computers [21, 22].

**2. Handling Status Changes and Exceptions:**The Reasons Utility configuration directly impacts the dispatcher's primary oversight tool, the **Exceptions Window**. For statuses other than Ready, a status change generates an exception that requires dispatcher input unless the reason code is configured for auto-acceptance [17, 23, 24]. The dispatcher often uses the utility's lists as a reference when manually correcting status exceptions if the operator chose the wrong code [9].

**3. Operational Importance (LP/DP Logic):**The distinction between equipment statuses is primarily driven by how the **optimization engine (LP and DP)** treats the asset [25]. The settings within the Reasons Utility govern this behavior:

• **Delay Status:** If a loading unit is on a delay, the assignment engine checks the delay type (configured via the Reasons Utility/related interfaces) to decide if trucks should be assigned to it, possibly timing the arrival just as the delay ends [10, 26, 27].

• **Down/Standby Status:** Equipment in Down or Standby statuses are generally **excluded** from the optimization calculations [25, 28]. Correct use of reason codes ensures accurate non-productive time is captured, influencing maintenance and productivity metrics [15, 29].

In essence, the Reasons Utility acts as the rulebook for defining the context of equipment downtime and availability, ensuring that data is consistently accurate for reporting and that the automated system responds appropriately to operational interruptions [30, 31].

--------------------------------------------------------------------------------

## DISPATCH Tiedown and Parkup Utility Management

The sources define the **Tiedown Utility** (also referred to synonymously as the **Parkup Utility**) as a critical interface for managing end-of-shift operations, and position it within the broader context of essential Dispatch system tools, specifically under the categorization of key utilities and modules related to shift change and location management.

Overview and Functionality

The **Tiedown Utility** is a key system utility in DISPATCH that allows Mine Controllers to **manage end-of-shift truck assignments to tiedown locations** [1], [2]. The terms **Shiftchange**, **Tiedown**, and **Parkup** are considered synonymous within this context [3], [4], [5], [6], [7].

The fundamental purpose of managing tiedown assignments is to **allow trucks to continue their production cycles as long as possible before parking** [8], thereby helping to avoid the non-productive time resulting from an early tiedown or the costly overtime incurred from a late tiedown [9].

Location within System Utilities

The Tiedown Utility is recognized as a fundamental system component and is accessible through several primary interfaces within the DISPATCH system:

1. **DISPATCH Master Keypad:** The utility is explicitly listed on the Master Keypad under the name **Parkup** [10]. The utility's form name is also cited as `parkup` [11], [12].

2. **MineView Application:** It is accessible via the Navigation Panel, typically listed under the **Shift Change** module, where other related utilities like **Shovel Tiedown** and **Tiedown Queues** are found [13].

3. **FormView Window:** Users can start the utility by entering its form name (`parkup` or `parkup.frm`) in the Execute Form field [11], [12], [14].

Tiedown Assignment Management and Operational Status

The utility supports detailed procedures for managing equipment status transitions during shift changes:

• **Tiedown Locations:** A tiedown location is defined as an area where equipment (primarily trucks) **park at the end-of-shift for operator change** or to transition to the **Standby** status if they are not scheduled for the upcoming shift [1], [15]. These locations are considered part of the Location Class hierarchy, alongside shops, dumps, and callpoints [16].

• **Status Transition:** In mines that enforce a break for shift change, equipment status automatically transitions from **Ready** to **Shiftchange** as the departing operator logs off [4]. Conversely, if an operator logs off equipment not scheduled for the next shift, they should enter **Standby** and a reason code so DISPATCH does not include that equipment in the new shift's calculations [15].

• **Operational Inclusion:** Equipment marked as **Shiftchange** is specifically notable because DISPATCH **still considers this equipment in its optimization and assignment calculations** in anticipation of the new shift logging on [17]. This proactive approach helps negate the need for the system to reroute trucks or adjust its plan if the incoming operator logs on slightly late [17].

Modes of Operation (Automatic and Manual)

The Tiedown Utility can function in two distinct modes to manage end-of-shift assignments:

1. **Automatic Mode (Auto Tiedown):** The system **automatically optimizes each truck’s last load assignment** for the shift and determines the parking location based on user-defined **tiedown procedures and variables** [1]. This mode requires defining the exact shift to which the procedure applies (e.g., Day Shift prepares equipment for Night Shift) and specific **Start Time**, **End Time**, and **Duration** for the procedure [18], [19].

2. **Manual Mode (Manual Tiedown):** This mode bypasses the automatic procedures, allowing the Mine Controller to make manual tiedown assignments based on real-time data displayed in the utility [20], [21]. In manual mode, controllers can **override a truck’s final assignment** and send the truck to a selected tiedown location immediately [22], [23]. Even in automatic mode, the Controller can manually override an assignment using Section 5 of the utility, though **DISPATCH still controls** **when** **the assignment is executed** [24], [25].

The utility also allows defining how the time spent in shift change affects maintenance tracking via the `MAINTTIEDOWN` variable, dictating whether this time is added to the equipment's service clock [26], [27], [28], [29].

--------------------------------------------------------------------------------

## Prestart Utility: Equipment Safety Checklist Management

The sources describe the **Prestart Utility** and its associated **Prestart Module** as a specialized safety and operational tool used to manage checklists that operators must complete during the equipment logon process, functioning as a key utility within the larger system.

Purpose and Function of the Prestart Module

The **Prestart Module** allows users to create **checklists of maintenance/safety items** that equipment operators are required to inspect and either pass or fail before commencing the shift [1, 2]. This utility enforces a procedural check to ensure equipment integrity prior to operation [3].

The ability to create unique checklists extends to various equipment types that possess a field computer, including trucks, excavators, auxiliary equipment, drills, and foreman vehicles [4]. For instance, a checklist for drills might include items like "Drill Bit" or "Oil Pressure," while a truck checklist might include "Tires" or "Air Conditioning" [4].

Key functions and outputs of the module include:

1. **Display and Input:** The checklist appears on the equipment's field computer immediately after the operator successfully logs on [1, 4]. Operators interact by pressing the item blocks to toggle them from **Fail** to **Pass** [1, 5]. Operators can also choose to **Pass All** items or enter a **general comment** if needed [5, 6].

2. **Required Actions:** Checklist items can be configured to trigger specific actions if they fail inspection, including [7]:

• **Record Item:** The system simply records the pass/fail status [7].

• **Contact Dispatcher:** Generates an **exception** on the dispatcher’s Exceptions screen if the item failed, requiring immediate attention [7].

• **Do Not Operate:** Records the failure and displays a **“do not operate” message** on the field computer, preventing the use of the equipment [7].

3. **Data Management:** The system stores the results (pass/fail status and comments) centrally in the database after the operator presses **OK** [5, 6, 8]. This data feeds **prestart reports** that Controllers can use to review prestart history on a shift-by-shift basis [9, 10].

Procedural Implementation and Configuration

Configuration of the prestart checklist is essential and involves the following steps, primarily undertaken by the Builder or system administrator:

1. **Checklist Creation:** You specify the type of equipment or equipment class (e.g., KOM 830E trucks) for the checklist [11]. You then create the individual items (up to 64 items) that make up the checklist [7, 12, 13].

2. **Setting Defaults:** The checklist items are typically set to **Fail by default** [1, 9]. This ensures the operator must actively confirm each item by interacting with the screen [9].

3. **File Generation and Upload:** Once the items and their required actions are defined, **GC config files** must be created for the checklist [14]. These configuration files are proprietary to the MineStar system and must be loaded onto the **onboard computers (CGCs)** of all specified equipment units, sometimes via a wireless network command or through physical file loading [15, 16].

4. **Modification:** If a checklist item needs to be changed (e.g., description, required action, or order), the checklist is modified in the **Prestart Utility**, and a new GC config file must be generated and loaded onto the equipment to reflect the changes [17, 18].

The benefit of using this module is **paperless recording** of checklists, immediate **dispatcher notification** of failures (allowing quick operator reallocation), and central updating of the configuration via the wireless network [19].

Prestart Utility in the Context of Key Utilities

The **Prestart Utility** (`prestart` or `prestart_item_list` form names) is a specialized tool accessible through the main user interface components of the DISPATCH system [3, 20]:

• **DISPATCH Master Keypad:** The utility is explicitly listed on the Keypad for quick access [20, 21].

• **FormView Window:** It can be launched by entering its form name [20, 22].

• **Navigation Panel/Control Panel:** The concept of the prestart process is considered fundamental enough to be listed on the key navigation screens [21, 23].

It directly supports the core **Shift Startup** process by ensuring safety and maintenance checks are completed immediately after an operator logs on [24, 25]. Resolving issues flagged by the Prestart Module is one of the **exceptions** that the dispatcher must handle immediately to minimize disruption to the haulage cycle [25]. Therefore, the Prestart Utility is critical because it proactively checks equipment readiness before it enters the stream of assignments, ensuring that only certified and functional equipment contributes to production [24, 26].

--------------------------------------------------------------------------------

## Mine GPS Utility and Spatial Data Management

The sources detail the **GPS Utility** as a specialized tool within the overall system utility suite, primarily focused on the management, configuration, and diagnostics related to the Global Positioning System (GPS) infrastructure and location tracking in the mine.

Primary Functions of the GPS Utility

The **GPS Utility** provides several distinct capabilities essential for maintaining the integrity and accuracy of the mine's spatial data and tracking system:

1. **Viewing and Changing Virtual Beacon Configuration Data:** The utility allows users to **view virtual beacon configuration data** for both shovels and general mine locations [1]. Users can change this configuration data directly within the utility [1]. The utility displays key beacon parameters such as the beacon ID, coordinates (easting and northing), type (e.g., End Point, Way Point), and radius coverage [1]. Changes made to beacon configuration, such as clicking the `Arrive` field, update the DISPATCH database [1].

2. **Querying Equipment Locations:** The GPS Utility includes options to query the **current easting and northing coordinates** (locations) for specific trucks, excavators, and auxiliary equipment via radio transmission [2], [3], [4], [5].

• The **Truck Loc. Option** queries and displays the current Easting and Northing coordinates for individual trucks, updating when the radio request is answered and the database is updated [3], [6].

• The **Excav. Loc. Option** queries and displays the location of excavators, providing information on the last reported coordinates, the current assigned location, and the closest alternative location, along with distances to help evaluate potential reassignments [4], [7].

• The **Aux. Loc. Option** performs a similar query for auxiliary equipment, displaying current Easting and Northing coordinates [5], [8].

3. **Coordinate Conversions:** The **Conversions Option** within the GPS Utility allows for the conversion of coordinates between three primary formats: **longitude/latitude, mine coordinates, and MMS Grid coordinates** [2]. This functionality is critical for verifying coordinate translation, a necessary step often performed during system installation [2], [9].

4. **Managing Polygon Beacons:** The **Polygon Beacons Option** is available for advanced users and project engineers to manage non-circular beacon configuration data, query if a test point location is within a polygon beacon, and import or delete polygon beacons [8], [10], [11].

GPS Utility in the Context of Key Utilities

The GPS Utility falls under the broad suite of tools used for operational control and database management within the DISPATCH system [12], [13].

• **Access:** The GPS Utility can be accessed directly from the **DISPATCH Master Keypad** [14], [15], [1], or by entering its form name (`gps`) via the **FormView Window** [16].

• **Purpose within Optimization:** The accurate geographical data managed by the GPS Utility is foundational to the efficiency of the overall system. GPS data ensures the system knows where equipment is located at all times and enables functions such as Best Path (BP) route calculation and dynamic reassignment [17], [18], [19]. If beacon configuration is poor or inaccurate (e.g., overlapping boundaries or incorrect locations), it can lead to truck tracking problems, misroute errors, and ultimately degrade the assignment optimization process [20], [21], [22].

• **Relationship to Location Management:** While the **Location Utility** manages fixed-point locations (like creation and deletion) [23], [24], the GPS Utility specifically manages the technical aspects of positioning beacons and coordinate data associated with those locations, particularly for GPS-enabled operations [18], [25]. The GPS Utility is integral to managing the virtual beacon network that facilitates equipment tracking, arrival and departure monitoring, travel time determination, and dynamic reassignment [26], [27], [28].

--------------------------------------------------------------------------------

DISPATCH Equipment Statuses and System Logic

The sources emphasize that **Equipment Statuses** are foundational data elements within the DISPATCH system, critical for operational control, planning, reporting, and, most importantly, driving the automated logic of the dispatch and optimization algorithms. These statuses directly dictate how various system utilities and interfaces must interpret, manage, and respond to the real-time condition of mining equipment.

The main operational statuses are **Ready, Down, Delay, Standby, and Shiftchange** (or Tiedown/Parkup) [1-3].

1. Equipment Statuses as Input for System Logic

The core purpose of recording equipment status is to ensure the accuracy of the mine model, which in turn allows the automated systems, like the optimization engines (Linear Programming/LP and Dynamic Programming/DP), to function effectively [4-8].

|   |   |   |
|---|---|---|
|Operational Status|System Perception & Impact|Key Utilities/Interfaces Involved|
|**Ready**|Equipment functions properly and **is considered** in optimization/assignment calculations [1, 7].|Essential for Fleet Update Assistant (FUA) and Dispatch Utility checks before assignments begin [9].|
|**Delay**|Equipment is temporarily interrupted but **is generally considered** in calculations, often resulting in a late Expected Time of Arrival (ETA) [7, 10, 11].|Delay Monitor, Delay Assistant, and Truck Cycle Editor are used for management, correction, merging, and splitting records [12-16].|
|**Shiftchange/Tiedown/Parkup**|Equipment is temporarily unavailable (e.g., during operator change) but is **considered in planning**, anticipating late logons and avoiding plan adjustments [3, 17].|Tiedown Utility and Dispatch Utility are used for setting and adjusting these end-of-shift assignments [18, 19].|
|**Down**|Equipment is unavailable due to mechanical/electrical failure and is **immediately excluded** from most optimization and assignment calculations [1, 7, 20].|Exceptions Window, Maintenance Utilities, and Down Equipment Utility handle reporting, acceptance, and management of the event [20-22].|
|**Standby**|Equipment is available but unused (e.g., due to lack of work/operator) and is **excluded** from optimization/assignment calculations [23].|TruckMod Utility, ShovelMod Utility, and Auxiliary Equipment Utility are used to set this status, often at the end of a shift [24-26].|

2. Management and Interaction via System Utilities and Interfaces

The DISPATCH system relies heavily on specific utilities and interfaces to manage status reporting and integrity:

A. Data Configuration and Integrity Tools

• **Reasons Utility:** This utility is central to defining and managing the **reason codes** associated with Down, Ready, Standby, and Delay statuses [27-29]. Accurate use of reason codes ensures correct documentation and planning [10, 23, 30]. For example, the utility defines the expected duration time (in minutes) for Delay reason codes, which influences how the optimization engine treats the equipment [31, 32].

• **Time Usage Model (TUM):** Statuses feed into the mine's customized TUM, which breaks down calendar time into categories like Available Time (including Operating Time, Operating Delay, and Operating Standby) and Down Time (including Planned Loss and Breakdown Loss) [4, 33, 34]. This categorization is crucial for accurate accounting and planning [4, 34, 35].

• **Shiftedit Utility:** This database management tool is used to display, create, edit, and delete **status records** historically, which is necessary if the DISPATCH system was down or if errors occurred in records during the shift [36-39].

B. Real-Time Monitoring and Control Interfaces

• **Exceptions Window:** This critical interface monitors unexpected changes in equipment status (Down, Delay, Standby, or Ready after Downtime/Standby) [20, 21, 40]. The Mine Controller must manually **Accept (F3)** or **Reject (F4)** most status change exceptions for the status change to officially take effect, unless the reason code is configured for automatic acceptance [30, 41-43].

• **Fleet Update Assistant (FUA):** This is the easiest place to view the availability for all machines [44]. Controllers primarily use FUA to check and manage the "Available for Assignment" flag, ensuring that equipment necessary for the production plan is selected [9, 45, 46].

• **Assistant Utilities (Truck, Shovel, Auxiliary Equipment):** These pages allow Controllers to display and quickly edit key information, such as the machine’s status, and to perform manual actions like changing the status of a truck or shovel when the operator cannot (e.g., due to field computer malfunction) [25, 47-49].

C. Operational Impact and Decision-Making

• **Assignment Engine Logic:** The status dictates whether the optimization models (LP and DP) include the equipment in their calculations [1]. For instance, equipment in **Delay** status remains factored into calculations, and the system may assign trucks to a loading unit even if it is delayed, anticipating the delay will end before the truck arrives [10, 50]. Conversely, equipment marked **Down** or **Standby** is removed from these calculations to prevent assignment failures [1, 23].

• **Onboard Systems (Field Computers):** Operators initiate status changes directly on the field computer (e.g., pressing **Down** or **Delay** and entering a reason code) [10, 29, 51]. This immediately sends data packets (TMAC messages) to the central system for processing and recording [52, 53]. If the onboard system malfunctions, the dispatcher uses tools like the Dispatch Utility to perform actions (**Schedule Action**) on behalf of the operator to update the status remotely [48, 54].

In summary, equipment statuses are the foundation upon which real-time operational decisions are built. The various DISPATCH system utilities and interfaces serve as the configuration, management, and troubleshooting layers necessary to ensure that the actual physical state of the equipment is accurately reflected in the digital mine model, maximizing productivity and minimizing disruptions [35, 55, 56].

--------------------------------------------------------------------------------

## Defining the Ready Status in Mine Optimization

The sources consistently define **Ready** as the fundamental and most desirable operational status for equipment and facilities, signifying immediate availability for use and confirming its inclusion in the crucial optimization and assignment calculations performed by the system.

Defining the Ready Status within Mine Operations

The **Ready** status is one of the primary operational statuses recognized by the DISPATCH system, alongside Down, Delay, Standby, and Shiftchange. Equipment and facilities designated as Ready **function properly and are actively in use**. This status falls under the broader category of **Operating Time** within the Time Usage Model, indicating that the equipment is currently engaged in productive mining operations or is prepared to be so engaged.

The goal of shift preparation is explicitly to ensure that all required machines (trucks, shovels, etc.) and locations (dumps, crushers, shops, fueling areas) have a status of **Ready**, **Shiftchange**, or **Delay**, but explicitly **not Down or Standby**. This availability status is crucial because the system must know which equipment units and locations are available for use so that it can accurately consider them in its optimization algorithms when generating haul truck assignments.

Central Role in Optimization and Assignment

The **Ready** status serves as the signal to the core optimization layers that an asset should be immediately considered for utilization:

1. **Inclusion in Calculations:** Equipment and facilities with the **Ready** status are **considered in the system's optimization and assignment calculations**. For example, DISPATCH sends Ready trucks to Ready shovels and dumping points. Similarly, location types such as dumps, crushers, and stockpiles must be **Ready** to ensure that DISPATCH assigns trucks to them.

2. **LP Model Integration:** When equipment is Ready, the Linear Programming (LP) layer actively incorporates it into the creation of the theoretical master plan (LP solution) for maximizing overall truck productivity [1]. The LP calculation uses the available equipment (Ready plus Delay) to determine haulage flow paths and feed rates that aim to meet production goals efficiently [2], [3].

3. **Dynamic Assignment (DP) Enforcement:** The Dynamic Programming (DP) model executes the LP solution in real time by assigning available trucks to shovels and dumping locations. A primary factor DP considers is the status of the equipment: Ready shovels are active destinations for truck assignments, and Ready trucks are considered available resources for loading and hauling tasks.

Transitioning to the Ready Status

The process for equipment reaching the Ready status is crucial for the start of a shift or resumption of service:

• **Initial Shift Start:** At the start of a shift, an operator logs on to the equipment, and the mobile device changes the equipment status from its previous state (often **Shiftchange** if the mine uses a shift break) to **Ready**. For shovels, the operator typically presses the Ready icon to notify DISPATCH that the shovel is prepared to begin digging at its current location [4], [5].

• **Post-Maintenance/Down/Standby:** If equipment was previously **Down** (due to mechanical failure) or **Standby** (due to no operator or lack of work), accepting a **Ready** exception in the office (or the operator pressing the Ready button) informs the system that the equipment is ready to return to production [6], [7]. Answering shovel Ready exceptions promptly is vital so DISPATCH can immediately consider the equipment in its optimization calculations [7]. The release of equipment from maintenance immediately changes the status to **Standby**, which then must be subsequently transitioned to **Ready** to resume service [8], [9].

• **Impact of Late Logons:** If an operator logs on minutes after a shift begins, and the equipment was previously in the **Shiftchange** status, DISPATCH will have already included that equipment unit in its dispatching scheme in anticipation of the logon, thereby **negating the need for rerouting or adjusting the overall plan** to accommodate the slightly late shift activation [10].

In essence, the **Ready** status is the operative indicator that signals to the entire optimization system that an asset is available, functioning, and ready to contribute to meeting the mine's production targets, forming the core operational basis for automatic assignment decisions.

--------------------------------------------------------------------------------

DISPATCH Equipment Down Status Management and Impact

The sources extensively detail the **Down** status as a primary equipment operational status, defining its causes, its significant impact on assignment calculations, and the precise procedures required for its management.

Definition and Context of the Down Status

**Down** is one of the five essential equipment operational statuses (Ready, Down, Delay, Standby, and Shiftchange) recognized by the DISPATCH system [1]. Equipment placed in the Down status is characterized by a mechanical or electrical failure, meaning it does **not function properly and is not currently in use** [1, 2].

Within the Time Usage Model, **Down Time** is defined as the total hours the equipment is **not available for work** [3]. This category is further subdivided into **Planned Loss** (scheduled maintenance downtime) and **Breakdown Loss** (hours unavailable due to a breakdown while scheduled to work) [3, 4].

The importance of using this status correctly is rooted in maintaining an accurate representation of the equipment's condition, which is critical for optimization algorithms [5].

Impact on Optimization and Assignments

The Down status has the most immediate and critical impact on the assignment engine:

• **Exclusion from Calculations:** When equipment is marked as Down, **DISPATCH immediately stops considering it** in its optimization and assignment calculations [1, 6]. This ensures that haulage resources are not allocated to unproductive machines [6]. Equipment in the Down status is effectively removed from the production plan calculations [7, 8].

• **LP Solution Impact:** The Linear Programming (LP) model, which creates the theoretical master plan (LP solution), only calculates and reports the optimal production paths and feed rates based on **available equipment** [9, 10]. Therefore, equipment that is Down is automatically excluded from the LP solution [11-14].

• **Crusher Downtime:** If a crusher goes Down, its material processing capacity is significantly reduced. This reduction causes DISPATCH to lower the number of trucks assigned to the relevant ore shovels, prioritizing the lowest priority shovels first [15]. However, DISPATCH does **not reroute trucks already en route** to the Down crusher [15].

• **Shovel Downtime:** When a shovel goes Down, DISPATCH instantly stops assigning trucks to it and automatically reassigns trucks already headed to the Down shovel [6]. The controller receives a Down exception, which includes options for manually reassigning trucks or choosing to let the trucks remain where they are [2, 16].

Procedural Management of the Down Status

Proper management of the Down status is largely the responsibility of the operator and the mine controller:

1. **Operator Action and Reason Code:** When equipment breaks down, the operator presses the **Down** button on the field computer and enters a **reason code** (a number representing the cause) [2, 17]. If the operator fails to enter a reason code, the controller must enter one through the Exceptions window [15, 18].

2. **Exception Handling:** A change to Down status **generates an exception** on the Exceptions window that the dispatcher **must manually accept or reject** for the status change to take effect [6, 19, 20].

3. **Troubleshooting and Options:** When a shovel Down exception is accepted, the system generates a second part providing crucial options, such as manually reassigning specific trucks, setting another spare shovel to **Ready**, or allowing DP to automatically reassign all affected trucks [16, 21]. If the dispatcher accepts the exception without using any specific options, the system defaults to automatically reassigning the trucks [22].

4. **Duration Management:** While Down status is intended for severe unavailability, a lengthy maintenance **Delay** (e.g., lasting more than one hour, or long enough for the operator to be reassigned) **should be changed to Down** status so that the equipment is removed from optimization calculations [23].

5. **Releasing Equipment:** Once maintenance is complete, the maintenance team clicks the **Release** link (in the Maintenance Down Equipment List screen). This changes the status of the equipment to **Standby** and generates a notification to the dispatcher that the equipment is ready to return to service [24].

**Key takeaway on Down Status:** The Down status is explicitly used to signify unavailability due to failure, leading to the equipment's complete removal from algorithmic considerations to ensure the integrity and effectiveness of the mining optimization models [1, 25, 26].

--------------------------------------------------------------------------------

## Mine Equipment Delay Status and Optimization

The sources provide extensive detail on the **Delay** status, defining it as a crucial component of equipment operational status, primarily used for tracking scheduled or unscheduled interruptions to productive work, such as blasting, operator breaks, and movement.

Definition and Operational Context of Delay

The **Delay** status represents a period when a mine entity (like a truck or road segment) is temporarily unproductive due to either scheduled or unscheduled interruptions to work [1]. It is categorized within the overarching **Available Time** and specifically as **Operating Delay** in the Time Usage Model [2]. This means that although the equipment is temporarily interrupted, it is still considered available for operation [3, 4]. The proper management of delays is considered a critical operational aspect for accurate reporting and planning purposes [2, 5].

The system recognizes several types of delays falling under this category, often customized by the mine site [5, 6]. Examples explicitly mentioned include:

• **Operator Breaks:** Delays for routine activities like **lunch**, **crib**, or **smoke** breaks are typically contained within the "Operational standby" category of delays [7]. Specific examples include codes for "LUNCH" and "OPERATOR BREAK" [8, 9].

• **Blasting:** The delay reason code for **blast** events is often classified under "Operational standby" [7].

• **Movement/Relocation:** Delays associated with equipment **movement** or repositioning are recognized, such as "cleanup," "reposition," or a "SHOVL MOVE" delay [7-9]. Operators may press **Move** (for short moves) or **Tram** (for long moves) on the field computer to register a delay before relocating a shovel [10, 11].

Delay Characteristics and Impact on Optimization

The core functionality of the Delay status revolves around managing temporary downtime with predictive planning:

1. **Duration and Intent:** Delays are generally intended for **short periods**, typically defined as twenty minutes or less, or when repair work is underway in the field but the total expected downtime is unknown [12, 13]. If a maintenance delay is expected to be lengthy (e.g., more than one hour), it should typically be changed to a **Down** status so that the equipment is removed from optimization calculations [14].

2. **Assignment Engine Consideration:** Unlike equipment placed in **Down** or **Standby** status (which are typically removed from calculations), equipment marked as **Delay** is generally still considered in the optimization and assignment calculations [12, 15-17].

3. **Predictive Assignment Logic:** The system leverages the duration associated with a delay. If a piece of loading equipment goes on a delay for a known period (e.g., 15 minutes), the assignment engine evaluates this and **may continue to send trucks to that loading unit**, anticipating that the trucks will arrive around the time the delay ends [6, 18]. This prevents unnecessary waiting by the loader after the delay concludes [18].

4. **Truck Flow:** For trucks specifically, when they are Delayed, the system still **expects the truck to arrive at its destination**, though not necessarily at the original estimated time [12]. Crusher Delays typically do not impact the flow of trucks [12].

Managing and Configuring Delays

The accuracy of delay records is paramount for production metrics (KPIs) and planning [1, 2, 19-25].

• **Customization and Grouping:** Delay records must accurately represent the event [1]. Delays are organized using **Delay Categories** (e.g., Operational, Operational standby) and specific **Delay Types** (e.g., Refuel, Blast) [6, 7, 26, 27]. Builders are responsible for creating and configuring these categories and types to match the site’s Time Usage Model (TUM) [22, 23, 27, 28].

• **Assignment Behavior Configuration:** The configuration of a delay type determines its influence on the assignment engine [21, 29, 30]. The critical setting is the **Assignment Delay** checkbox: if checked, the delay makes the target entity unavailable for assignment, factoring the delay into the planning [22, 31-35]. For instance, a truck coming off a "lunch" delay may receive an assignment, while one coming off a "waiting for equipment" delay may not [21].

• **Handling Delay End Times:** Setting an **Estimated Duration** when configuring a delay type allows the assignment engine to "expect" the delay to end at that time, helping plan future assignments [36]. If the duration is set to unknown or unscheduled, the system does not anticipate the machine returning at a specific time and generally ignores it for future scheduling [36].

• **Monitoring and Correction:** The **Controller** is responsible for monitoring and managing delays using tools like the **Delay Monitor** and **Delay Assistant** [28, 32, 37, 38]. If an operator selects the wrong delay, the Controller must correct the record, ensuring data integrity [38-40]. It is crucial that Controllers address inaccuracies, such as time gaps between consecutive delays, usually by starting the second delay before manually stopping the first, allowing the system to automatically close the gap [41-43].

• **Maintenance Tracking:** The system monitors delay durations. If a delay (such as planned maintenance) is configured to track against maintenance time, the duration of the delay is added to the equipment’s service clock [44-46]. If the flag is set to NO, the system checks the reason code's maintenance time flag for direction [47].

--------------------------------------------------------------------------------

## Standby Status in Mine Operational Systems

The sources consistently define **Standby** as a critical operational status for equipment, primarily denoting lack of work or operator availability, and emphasize that equipment in this status is generally excluded from optimization and assignment calculations.

Definition and Context within Operational Statuses

**Standby** is one of the five major equipment operational statuses recognized by the DISPATCH system, alongside Ready, Down, Delay, and Shiftchange [1]. The primary reasons for an equipment unit to be placed in the Standby status are a **lack of work** or **no operator availability** [2]. Equipment units in Standby are considered **Operating Standby** within the broader Time Usage Model, meaning the equipment is technically available for operation but is currently not being utilized [3].

The purpose of maintaining accurate status records, including Standby, is vital for accounting, planning, and ensuring accurate data is used by the system for availability and utilization calculations [4-6].

Impact on Optimization and Assignments

The key defining characteristic of the **Standby** status is its effect on the system's optimization processes:

• **Exclusion from Calculations:** Equipment units (such as trucks, shovels, or crushers) marked as **Standby are generally not considered** in the system's optimization and assignment calculations [1, 2, 7]. This prevents the system from generating assignments for unavailable machines and ensures resources are allocated efficiently among active equipment [7].

• **LP and DP Logic:** In the Modular Mining optimization strategy (DOS), the Linear Programming (LP) model and Dynamic Programming (DP) model rely on having up-to-date information [8, 9]. When equipment is on Standby, it is removed from consideration by the LP model when calculating the optimal theoretical master plan (LP solution) for truck flow and feed rates [10-13]. If the `Allow Unused Shovels in Undertrucked Situations (LPMATCHTRUCKS)` parameter is set to `NO`, the LP model assumes unlimited haulage resources and ignores priorities and proportionality, but setting a shovel to Down or Standby ensures it is removed entirely from LP calculations [11, 14-17].

Procedural Management and Monitoring

**Initiation of Standby Status:**

• **End of Shift:** The Standby status is commonly used at the **end of a shift** to mark equipment not scheduled for the upcoming shift [7, 18]. This proactive step saves the oncoming dispatcher time during shift preparation [7, 18].

• **Change from Delay:** If a machine that was temporarily Delayed (e.g., for Shiftchange or a short task) is subsequently determined to be out of service due to lack of work or an operator, its status must be correctly changed to Standby. The specific scenario of a **Shift Change delay** transitioning to **Standby No Operator** emphasizes that this must be done without creating a time gap between the delays to maintain data integrity [19, 20].

• **Manual Intervention:** A Controller can manually change an equipment unit's status to Standby using various utilities, such as the **Dispatch Utility**, the **TruckMod Utility**, or the **Auxiliary Equipment Utility** [21-25]. When manually setting Standby, the Controller must also ensure a **reason code** is entered to describe the specific cause, or the system will flag the event as an exception requiring dispatcher input [26-30].

**Consequences of Standby (Monitoring):**

• **Exceptions:** A change from any other status to Standby usually **generates an exception** that the dispatcher must address, especially if the reason code is not set to auto-accept [27, 29, 31-33].

• **Visibility:** Equipment on Standby generally **does not appear** on key operational displays like the Haul Route window (when Down or Standby) [34]. However, some utility views, like the `Shift Equipment Records Utility` and `Auxiliary Equipment Utility`, explicitly list equipment in Standby or Shiftchange status to allow for monitoring and data editing [35-38].

• **Shift Planning:** When reviewing shift preparation checklists, the dispatcher must ensure that trucks, shovels, and locations intended for use are not left in the Down or Standby statuses, as this negatively impacts assignment generation [7, 39-43].

In summary, the **Standby** status is crucial for managing operational time because it accurately flags equipment that is electronically available but logistically out of service (due to lack of work or operator), ensuring that the complex optimization algorithms (LP and DP) only focus on productive resources, thereby maintaining the **accuracy of the mine's operational model** [4, 8, 9, 44].

--------------------------------------------------------------------------------

## Shiftchange and Equipment Operational Status Management

The sources provide a comprehensive overview of **Shiftchange**, viewing it synonymously with **Tiedown** and **Parkup**, and highlighting its crucial role in managing equipment status, particularly in anticipation of late logons [1-3].

Shiftchange and Operational Statuses

The **Shiftchange** status is one of the key operational statuses recognized by the system, alongside **Ready**, **Down**, **Delay**, and **Standby** [4, 5]. The overall objective of managing equipment status is to accurately track the time equipment spends working, available, or unavailable [6].

When a piece of equipment is placed in the **Shiftchange** status, it falls under a broader operational category typically labeled as **Operational standby**, indicating that while the equipment is currently interrupted, it is prepared to resume productive work [7, 8].

The goal of establishing shift change procedures (tiedown strategies) is fundamentally operational: to allow trucks to **continue producing until late in the shift**, thereby maximizing productivity and avoiding excessive idle time or costly overtime that results from an early or late tiedown [9, 10].

The Role of Shiftchange in Managing Late Logons

The central function of maintaining the **Shiftchange** status relative to personnel changes is to ensure a smooth transition of operations, especially if the incoming operator logs on late.

The system specifically considers **Shiftchange** equipment in its optimization and assignment calculations _in anticipation of incoming workers logging on_ to the equipment [11]. This anticipation ensures that the equipment unit is already incorporated into the dispatching scheme. If an operator logs on minutes after the shift has started, the system has effectively **preemptively adjusted its planning**, which **negates the need for the system to reroute trucks or adjust its overall plan to accommodate the late logon** [11].

If an operator does log on late or is simply having trouble performing the logon procedure, the dispatcher can intervene using the Dispatch Utility to manually press the Logon button on behalf of the operator, thereby adjusting the system status to reflect the operator's presence and allowing the unit to become productive [12-14].

Procedural Management of the Shiftchange Status

The **Tiedown Utility** is the dedicated interface used by the Controller to manage end-of-shift truck assignments to tiedown locations [2, 10].

1. **Status Initiation:** In mines that utilize a production break for shift change, the equipment status automatically changes from **Ready** to **Shiftchange** as the departing operator logs off the equipment [1].

2. **Assignment and Location:** The equipment is directed to a designated tiedown location (or park-up area) where the operator change takes place [10, 15, 16]. A default tiedown location or station can be configured for trucks and loading tools, streamlining the end-of-shift process [17, 18].

3. **Post-Shift Delay Management:** If a machine that was assigned a **Shift Change delay** is not subsequently used in the new shift, the delay must be smoothly transitioned to **Standby No Operator** to accurately reflect its status as unavailable due to lack of work, not shift change [19]. It is critical that there are **no gaps between delays** during this transition, which is achieved by starting the new delay _before_ manually stopping the previous one; the system automatically resolves the end time of the former delay [19, 20].

4. **Assignment Behavior upon Completion:** When a truck finishes a shift change delay and becomes ready for work, the system is typically configured to immediately attempt to assign it a new haulage task [21].

Furthermore, system parameters govern whether time spent in the **Shiftchange** status impacts maintenance tracking. The variable `MAINTTIEDOWN` dictates if the shiftchange time should be added to the truck or shovel maintenance clock [22-25]. If this flag is set to 'NO', the system references the maintenance time flag associated with the reason code specified in the status change to determine if the time is added to the clock [23].

In essence, managing the **Shiftchange** status effectively converts planned transition downtime into anticipatory operational time, ensuring the continuity of the optimized plan regardless of minor delays in operator logging. This smooth conversion of resources is essential for achieving system goals and maximizing productivity [9, 11, 26].
