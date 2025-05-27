# Optimization of Task Assignment in Parallel Machines using FlexSim

## Introduction

This project focuses on optimizing the assignment of 20 different types of tasks to three parallel machines within a manufacturing or processing environment. Each task has a specific priority level (1 to 4), processing time per machine, and associated profit, while uncompleted tasks incur penalties. The machines are subject to pre-established downtimes (failures). The primary objective is to **maximize the net profit** by developing an effective task assignment strategy, considering operational constraints and machine failures, over a simulation horizon of 1000 minutes. This study compares a baseline scenario (current system with no optimization) against an improved scenario implementing a specific assignment rule. The simulation and optimization are performed using **FlexSim** software

---
## Architecture

The simulation model is built using **FlexSim (version 2024)**, a discrete-event simulation tool. The core components of the FlexSim model include:

* **Task Generation (`Source1`/`Source2`)**: Generates tasks (1000 tasks arrive at time 0) with types (1-20) assigned based on a triangular distribution (min 0.5, max 20.5, mode 7). Triggers assign priorities based on task type.
* **Queues (`Queue1`/`Queue3`, `Queue2`/`Queue4`)**:
    * Initial Queues (`Queue1`/`Queue3`): Store tasks from the source. A trigger here registers a penalty of 9 units for each incoming task, assuming it might not be processed.
    * Final Queues (`Queue2`/`Queue4`): Collect processed tasks. Triggers here update a global table for gains (based on priority) and decrement the penalty for successfully processed tasks.
* **Processors (`Processor1`/`Processor4`, `Processor2`/`Processor5`, `Processor3`/`Processor6`)**: Represent the three parallel machines. Each processor has specific processing times for different task types and is subject to defined failure schedules (TTFF, MTBF, MTTR). Processors check if they can handle a task type based on the processing time table; if not (time is 0 or '*'), the task is routed to another capable machine with the shortest processing time for that task type using "Port by case" logic.
* **Global Tables**: Used in FlexSim to manage:
    * Processing times and task priorities.
    * Gains and penalties to calculate net profit.
    * The IGUTD (Índice de Ganancia por Unidad de Tiempo Disponible) values for the improved scenario.

The simulation compares two main scenarios:
1.  **Escenario Actual (Base)**: Tasks are assigned to the first available machine that can process them, based on technical restrictions.
2.  **Escenario de Mejora (Optimized)**: Implements the **IGUTD (Índice de Ganancia por Unidad de Tiempo Disponible)** rule for task assignment.

---
## Features

* **Task Prioritization**: 20 task types with 4 priority levels, each influencing profit.
    * Priority 1: 3 units profit.
    * Priority 2: 5 units profit.
    * Priority 3: 10 units profit.
    * Priority 4: 18 units profit (total, implies 2 subtasks). In the model, this is simplified to a single task with 36 units profit to account for the two subtasks.
* **Subtask Handling (Priority 4)**: Priority 4 tasks require two subtasks to be completed, processed on the same machines enabled for the main task. In the simulation model, processing times for priority 4 tasks (types 2, 4, 7, 14) were doubled to represent the two subtasks.
* **Machine Constraints**:
    * Specific processing times per task per machine.
    * Technical restrictions prevent certain tasks from being run on specific machines (indicated by '*' or 0 in processing time tables).
* **Machine Failures**: Predefined machine downtimes (TTFF, MTBF, MTTR) following specific statistical distributions are incorporated.
* **Profit/Penalty System**:
    * Gains generated based on task priority.
    * Penalties of 9 units for each task or subtask not processed.
* **Simulation Horizon**: 1000 minutes.
* **Task Arrival**: 1000 tasks arrive at time 0, with task types determined by a triangular distribution.
* **Optimization Rule (IGUTD)**: A custom "Índice de Ganancia por Unidad de Tiempo Disponible" rule developed to maximize net profit by prioritizing tasks based on their economic return per unit of processing time on a specific machine.
    * `IGUTD = Ganancia Total de la Tarea / Tiempo de Procesamiento en la Máquina`

---
## Installation

To run this simulation project:

1.  **Software**:
    * **FlexSim Simulation Software (Version 2024 or compatible)** is required. Download and install from [FlexSim's official website](https://www.flexsim.com).
2.  **Project Files**:
    * Obtain the FlexSim model file (`.fsm`) and any associated data tables or supporting documents for this project. (Assuming these would be in a repository or shared folder).
3.  **Open the Model**:
    * Launch FlexSim.
    * Go to `File > Open Model` and navigate to the project's `.fsm` file.

---
## Usage

1.  **Understanding the Model**: Familiarize yourself with the model components in FlexSim (Sources, Queues, Processors, Global Tables) as described in the [Architecture](#architecture) section and the detailed report (`CASO3_MapaRodriguez_ZenenContreras_JuanParrales.pdf`).
2.  **Running the Simulation**:
    * Once the model is open, you can run the simulation using the control buttons in the FlexSim interface (e.g., Reset, Run, Stop).
    * The simulation will execute for a defined horizon of 1000 minutes.
3.  **Scenario Selection**:
    * The project likely contains two distinct models or configurations within FlexSim to represent the "Escenario Actual" and the "Escenario de Mejora (IGUTD)". Ensure you are running the desired scenario. The report uses FlexSim's Experimenter to compare scenarios.
4.  **Viewing Results**:
    * Key performance indicators (KPIs) like total net profit, tasks processed, Work In Progress (WIP), and losses are tracked, often in Global Tables within FlexSim or through dashboards.
    * The detailed report (`CASO3_MapaRodriguez_ZenenContreras_JuanParrales.pdf`) presents an analysis of these results.

---
## Configuration

The simulation model's behavior is configured through several parameters and data tables within FlexSim:

* **Task Arrival**: Set in the `Source` object, with 1000 tasks at time 0 and task types following a triangular distribution (0.5, 20.5, 7).
* **Processing Times**: Defined in a global table, specifying minutes per task type per machine. An asterisk (*) or 0 indicates impossibility.
    * For priority 4 tasks (types 2, 4, 7, 14), processing times are doubled in the model to account for two subtasks.
* **Task Priorities & Gains**: Defined in a global table, linking task types to priorities (1-4) and their respective gains.
    * Gain for Priority 4 tasks is set to 36 units in the model.
* **Machine Failures (MTBF/MTTR)**: Configured for each processor according to the specified distributions (TTFF, MTBF, MTTR).
* **IGUTD Rule (Improved Scenario)**:
    * A global table in FlexSim stores processing times and gains used to calculate IGUTD values.
    * Logic implemented in FlexSim (likely using Process Flow or triggers in Queue1/3) calculates `IGUTD = Gain / ProcessingTime` for each task-machine combination and assigns the task to the machine with the highest IGUTD.
* **Simulation Time**: Set to 1000 minutes.

---
## Dependencies

* **FlexSim Simulation Software**: Version 2024 (or a version compatible with the model file).

---
## Examples

### IGUTD Rule Calculation Example

As illustrated in the project report (`CASO3_MapaRodriguez_ZenenContreras_JuanParrales.pdf`, page 9):
Consider Machine 1 (M1) and three candidate tasks:

| Tarea | Prioridad | Ganancia | Tiempo en M1 | IGUTD         |
| :---- | :-------- | :------- | :----------- | :------------ |
| T4    | 4         | 18 *     | 70           | 0.257         |
| T9    | 1         | 3        | 70           | 0.043         |
| T18   | 3         | 10       | 16           | 0.625         |
(*Note: Report uses 18, model uses 36 for P4*)
Based on this, T18 would be chosen for M1 as it has the highest IGUTD (0.625), offering the most gain per unit of processing time.

### FlexSim Model Structure (Conceptual)

* **Scenario 1 (Base)**:
    * `Source1` -> `Queue1` -> `Processor1` / `Processor2` / `Processor3` (First Available Logic) -> `Queue2`
* **Scenario 2 (Improved - IGUTD)**:
    * `Source2` -> `Queue3` (IGUTD Logic Applied on Exit) -> `Processor4` / `Processor5` / `Processor6` (Assigned by IGUTD) -> `Queue4`
    (Processor numbering 4,5,6 for the improved scenario is based on diagrams in `CASO3_MapaRodriguez_ZenenContreras_JuanParrales.pdf`)

---
## Troubleshooting

* **Model Not Running/Errors**:
    * Ensure you are using a compatible version of FlexSim.
    * Check the FlexSim system console for error messages which can indicate issues with logic, table references, or object connections.
* **Incorrect Results**:
    * Verify all global table data (processing times, priorities, gains) matches the problem statement.
    * Double-check the implementation of machine failure parameters (distributions, values).
    * Confirm the IGUTD calculation and assignment logic in the improved scenario is correctly implemented as per the defined algorithm.
    * Ensure triggers in Queues and Processors for recording gains/losses are functioning correctly.
* **Performance**: Complex FlexSim models can be resource-intensive. Ensure your system meets FlexSim's minimum requirements.

---
## Contributors

* Maria Paula Rodríguez Ruiz
* Zenen Contreras Royero
* Juan Felipe Parrales

This project was developed by OptiTech Solutions for the Optimization and Simulation course, Department of Systems Engineering, Pontificia Universidad Javeriana, Bogotá, Colombia.

---
## License

This project was developed for educational purposes within an academic course. Specific licensing details should be confirmed with the contributors or the overseeing academic department.
