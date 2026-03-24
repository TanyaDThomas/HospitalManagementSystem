# Hospital Management System
Hospital Management System enabling patients to book appointments, doctors to document visits with SOAP notes and prescriptions, and staff to manage scheduling and check-ins.

---

## Plans Ahead

### WHY
Healthcare is big where I am located.I wanted to build a real-world, domain-complex application, from planning and database design through implementation, that solves a genuine operational problem in healthcare.

### Phase 1: Core Patient Flow
#### Modules: Patient Portal + Reception + Clinical

This delivers a complete system where a patient can book an appointment, check in at reception, see a doctor, and have that visit documented.

#### Step 1: User Stories (The "What")
Before figuring out what features I wanted to add, I needed to understand who needs what.

##### Patient Portal User Stories
New patient	Register with my personal info and insurance	I can create an account
Registered patient	Log in securely	My health information stays private
Patient	View available doctors and time slots	I can choose what works for me
Patient	Book an appointment	I can secure my preferred time
Patient	See my upcoming appointments	I remember when to come in
Patient	Cancel an appointment	I free up the slot for others
Patient	Reschedule an appointment	I can change plans without calling
Patient	Receive a reminder	I don't forget my appointment
Patient	View my past visits	I can recall what happened
Patient	See my lab results	I understand my health status


##### Reception User Stories
Receptionist	Search for a patient by name or ID	I can quickly find them
Receptionist	Check in a patient	The doctor knows they arrived
Receptionist	Verify insurance information	Billing is accurate
Receptionist	Assign a room	The patient knows where to go
Receptionist	View today's schedule	I know who is coming and when
Receptionist	See room availability	I know where to place patients
Receptionist	Mark a patient as checked out	The visit is complete
Receptionist	Book appointments over phone	Patients without internet can still schedule


##### Clinical User Stories (Doctors & Nurses)
Nurse	See checked-in patients	I know who needs vitals taken
Nurse	Record vital signs	The doctor has current data
Nurse	Update patient status	Everyone knows where the patient is
Doctor	See my patient list for today	I can plan my day
Doctor	View patient medical history	I have context for today's visit
Doctor	See allergies and conditions	I avoid harmful treatments
Doctor	Record SOAP notes	I document the visit properly
Doctor	Add a diagnosis	The visit has a medical conclusion
Doctor	Write a prescription	The patient gets medication
Doctor	Order a lab test	I can get diagnostic information
Doctor	Complete a visit	Billing can be triggered

## Step 2: User Flows

### Flow 1: New Patient Registers and Books Appointment

#### Patient Booking Flow
![Booking Flow](docs/images/booking-flow.png)



### Flow 2: Complete Patient Visit 
#### Main Work Flow
![Booking Flow](docs/images/booking-flow.png)


### Flow 3: Cancel and Reschedule
![Booking Flow](docs/images/booking-flow.png)


## Step 3: Feature Breakdown (The "What Exactly")
For each module, list features with clear boundaries.

### Patient Portal Features
Feature	Description	Acceptance Criteria
Registration	New user account creation	Form validates all fields; email must be unique; password hashed; verification email sent
Login	Secure authentication	Email/password; lockout after 5 failed attempts; password reset flow
Profile	View/edit personal info	Name, email, phone, address all editable; insurance info editable
Book Appointment	Schedule new visit	Shows available doctors with specialties; shows 7-day calendar; prevents double-booking; confirmation generated
My Appointments	List upcoming visits	Shows date, time, doctor, location, status; actions available based on status
Cancel	Cancel upcoming appointment	Only allowed if >24 hours before; requires reason; sends confirmation
Reschedule	Change appointment time	Only allowed if >24 hours before; shows available slots; updates existing appointment
Past Visits	View visit history	Read-only list of completed visits with dates and doctors
Lab Results	View test results	Read-only; shows test name, date, result value, reference range, flag if abnormal

### Reception Features
FEATURE:
DESCRIPTION:
ACCEPTANCE CRITERIA: 
Patient Search	Find patient by name/ID	Search as-you-type; results show name, DOB, next appointment
Check In	Mark patient arrived	Requires appointment selected; records check-in time; changes status; assigns room from available
Check Out	Mark visit complete	Requires visit completed by doctor; records check-out time; triggers billing
Daily Schedule	View today's appointments	Calendar view; shows all appointments with status; filter by doctor
Room Management	View and assign rooms	Grid showing rooms and status; dropdown for assignment during check-in
Phone Booking	Schedule for patient	Same as patient booking; staff can book on behalf
Walk-in Handling	Create unscheduled visit	Creates new appointment; marks as walk-in; requires doctor with availability

### Clinical Features

FEATURE : Patient List	

DESCRIPTION : View assigned patients

ACCEPTANCE CRITERIA: Patient Search	Find patient by name	Quick search for records


FEATURE: Patient List
DESCRIPTION: View assigned patients
ACCEPTANCE CRITERIA: List by room and status (waiting, ready, in-progress, completed)

FEATURE:Patient Search
DESCRIPTION: Find patient by name
ACCEPTANCE CRITERIA: Quick search for records

FEATURE: EHR View
DESCRIPTION: Complete patient record
ACCEPTANCE CRITERIA: Shows demographics, allergies, conditions, past visits, medications, lab results

FEATURE: Vital Signs
DESCRIPTION: Record measurements
ACCEPTANCE CRITERIA: BP (two fields), heart rate, temperature, weight, respiratory rate; timestamped

FEATURE: SOAP Notes
DESCRIPTION: Structured visit documentation
ACCEPTANCE CRITERIA: Four sections (Subjective, Objective, Assessment, Plan); saved per visit

FEATURE: Diagnosis
DESCRIPTION: Add diagnosis codes
ACCEPTANCE CRITERIA: ICD-10 lookup or free text; primary and secondary

FEATURE: Prescriptions
DESCRIPTION: Write medication orders
ACCEPTANCE CRITERIA: Search medication catalog; dosage, quantity, instructions, refills

FEATURE: Lab Orders
DESCRIPTION: Request tests
ACCEPTANCE CRITERIA: Test catalog selection; priority (routine/urgent); clinical notes

FEATURE: Visit Completion
DESCRIPTION: End encounter
ACCEPTANCE CRITERIA: Validates SOAP note exists; updates status; triggers billing

FEATURE: Medical History
DESCRIPTION: View and update
ACCEPTANCE CRITERIA: Conditions, surgeries, medications, allergies; with dates where applicable

## Step 4: Database Schema Planning (High Level)
### Core Tables for Phase 1
#### Table	Purpose	Key Fields
Users	Authentication and base identity	Id, Email, PasswordHash, FirstName, LastName, Role

Patients	Patient-specific data	Id, UserId, DOB, InsuranceProvider, InsuranceMemberId

Doctors	Doctor-specific data	Id, UserId, Specialization, LicenseNumber

Appointments	Scheduling	Id, PatientId, DoctorId, DateTime, Status, RoomNumber, Reason

Visits	Clinical encounters	Id, AppointmentId, PatientId, DoctorId, Date, Status

Vitals	Measurements	Id, VisitId, BP_Systolic, BP_Diastolic, HeartRate, Temperature, Weight, RecordedAt

SOAPNotes	Clinical documentation	Id, VisitId, Subjective, Objective, Assessment, Plan

Diagnoses	Medical conditions for visit	Id, VisitId, Code, Description, IsPrimary

Medications	Drug catalog	Id, Name, GenericName, Strength, Form

Prescriptions	Medication orders	Id, VisitId, MedicationId, Dosage, Quantity, Instructions, Status

LabTests	Test catalog	Id, Name, Category, ReferenceRange

LabOrders	Test requests	Id, VisitId, TestId, Status, OrderedAt, Result, ResultDate

### Key Relationships

Users ────┬──→ Patients (one-to-one)
          └──→ Doctors (one-to-one)

Patients ───→ Appointments ───→ Doctors
                │
                └──→ Visits ───→ Vitals
                              ───→ SOAPNotes
                              ───→ Diagnoses
                              ───→ Prescriptions ───→ Medications
                              ───→ LabOrders ───→ LabTests
                              
## Step 5: Module Boundaries (What Each Owns)
Module	    Owns	          Reads from Others

Patient Portal	Registration, booking UI, patient profile	Appointment data, visit history, lab results

Reception	Check-in/out, scheduling UI, room assignment	Patient data, doctor schedules, appointments

Clinical	Vitals, SOAP notes, orders, prescriptions	Patient data, appointments, lab results

Rule: Clinical does NOT manage appointments. Reception does NOT write SOAP notes. Patient Portal does NOT change clinical data.

## Step 6: Success Criteria for Phase 1
Before moving to Phase 2 (Billing), verify these are complete:

Patient can register, log in, and book an appointment

Patient receives confirmation email

Receptionist can see appointment, check patient in, assign room

Nurse can see checked-in patient, record vitals

Doctor can see patient, view history, record SOAP note

Doctor can write prescription and order lab

Visit can be completed

Patient can log in and see past visit with notes and results

All role-based permissions work (Patient cannot see Reception views)

No data is lost or incorrectly linked between modules
