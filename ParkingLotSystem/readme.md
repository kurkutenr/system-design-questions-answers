# Parking Lot System Design
:car:  :parking: 

## Problem Statement
Design a multi-level parking lot system.

This design ensures efficient management of a multi-level parking lot with real-time tracking of available slots and optimized space utilization.

### Design Levels
- Multi-Level Parking Lot Layout
  - Entry/Exit Points
  - Ramps
  - Sections
  - Slot Types
  - Navigation Aids

### Classes and Data Structures
- `ParkingLot`
  - Attributes: `String id`, `List<Level> levels`, `int totalSlots`, `int availableSlots`
  - Methods: `parkVehicle(Vehicle vehicle)`, `unparkVehicle(Vehicle vehicle)`, `getAvailableSlot(VehicleType type)`, `updateAvailability()`

- `Level`
  - Attributes: `String id`, `List<ParkingSlot> parkingSlots`, `int levelNumber`, `int totalSlots`, `int availableSlots`
  - Methods: `getAvailableSlot(VehicleType type)`, `updateAvailability()`

- `ParkingSlot`
  - Attributes: `String id`, `SlotSize size`, `boolean isOccupied`, `Vehicle currentVehicle`
  - Methods: `assignVehicle(Vehicle vehicle)`, `removeVehicle()`, `isAvailable()`

- `Vehicle`
  - Attributes: `String licensePlate`, `VehicleType type`, `String ownerName`
  - Methods: `getType()`

- `VehicleType`
  - Enum: `COMPACT`, `LARGE`, `ELECTRIC`

- `SlotSize`
  - Enum: `SMALL`, `MEDIUM`, `LARGE`

### Vehicle Management
- Parking a Vehicle
  - Process
    - At entry, system checks for an available slot matching the vehicle type.
    - Allocates the slot and updates the slot status to occupied.
    - Records entry time for billing purposes.

- Unparking a Vehicle
  - Process
    - At exit, system retrieves vehicle information.
    - Frees up the slot and updates the slot status to available.
    - Calculates parking fee based on the duration.

- Efficient Space Utilization
  - Prioritize filling smaller slots with compact vehicles.
  - Reserve large slots for larger vehicles.
  - Designate specific slots for electric vehicles with charging points.

### Tracking Availability
- Real-Time Tracking
  - Use sensors (IoT) at each slot to detect occupancy.
  - Each slot updates its status in the system database in real-time.

- Centralized Monitoring System
  - Central system aggregates data from all levels and slots.
  - Displays current availability on digital boards at entry points and within the parking lot.

- Update Mechanism
  - Each level periodically updates its availability status to the central system.
  - Central system triggers notifications for slot availability changes.


### Class Diagram
+----------------+       +----------------+       +----------------+       +----------------+       +----------------+
|  ParkingLot    |       |  Level         |       |  ParkingSlot   |       |  Vehicle       |       |  VehicleType   |
+----------------+       +----------------+       +----------------+       +----------------+       +----------------+
| - id           |1     n| - id           |1     n| - id           |       | - licensePlate |       | + COMPACT      |
| - levels       |-------| - parkingSlots |-------| - size         |       | - type         |       | + LARGE        |
| - totalSlots   |       | - levelNumber  |       | - isOccupied   |       | - ownerName    |       | + ELECTRIC     |
| - availableSlots|       | - totalSlots  |       | - currentVehicle|       +----------------+       +----------------+
+----------------+       | - availableSlots|       +----------------+
| + parkVehicle()|       +----------------+       | + assignVehicle()|
| + unparkVehicle()|      | + getAvailableSlot()|   | + removeVehicle() |
| + getAvailableSlot()|   | + updateAvailability()| | + isAvailable()   |
| + updateAvailability()| +----------------+       +----------------+
+----------------+       


### Sequence Diagram for Parking a Vehicle
Driver -> ParkingLot: parkVehicle(Vehicle vehicle)
ParkingLot -> Level: getAvailableSlot(VehicleType type)
Level -> ParkingSlot: isAvailable()
ParkingSlot -> Level: return slot if available
Level -> ParkingLot: return slot if available
ParkingLot -> ParkingSlot: assignVehicle(Vehicle vehicle)
ParkingSlot -> ParkingLot: updateAvailability()
ParkingLot -> Driver: Vehicle parked at slot

### Sequence Diagram for Unparking a Vehicle
Driver -> ParkingLot: unparkVehicle(Vehicle vehicle)
ParkingLot -> Level: findVehicleSlot(Vehicle vehicle)
Level -> ParkingSlot: currentVehicle == vehicle
ParkingSlot -> Level: return slot if found
Level -> ParkingLot: return slot if found
ParkingLot -> ParkingSlot: removeVehicle()
ParkingSlot -> ParkingLot: updateAvailability()
ParkingLot -> Driver: Vehicle unparked