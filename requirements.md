# Requirements Document: Naavik

## Introduction

Naavik is an AI-powered, proactive, voice-first opportunity tracking system that helps students and job seekers—especially visually impaired and digitally underserved users—access public opportunities such as government exams, scholarships, and jobs. The system operates through a voice helpline where users share profile information, receive eligibility confirmations, and get proactive alerts when relevant opportunities are published.

## Glossary

- **Naavik_System**: The complete AI-powered opportunity tracking platform including voice interface, backend services, and notification system
- **Voice_Interface**: The telephony-based interaction system that handles incoming and outgoing calls
- **User**: A student or job seeker who interacts with the system via phone
- **Profile**: User information including demographics, education, location, and preferences
- **Opportunity**: A public notification for government exams, scholarships, jobs, or similar programs
- **Eligibility_Engine**: The component that matches user profiles against opportunity criteria
- **Alert_Registry**: The database of users registered for notifications about future opportunities
- **Notification_Service**: The component that proactively calls users when relevant opportunities are published
- **Opportunity_Scraper**: The component that monitors and extracts opportunity data from public sources
- **Call_Session**: A single interaction between a user and the Voice_Interface

## Requirements

### Requirement 1: User Profile Registration

**User Story:** As a user, I want to register my profile via phone call, so that the system can identify relevant opportunities for me.

#### Acceptance Criteria

1. WHEN a user calls the helpline, THE Voice_Interface SHALL greet the user and prompt for profile information
2. WHEN the user provides profile information, THE Naavik_System SHALL collect name, age, education level, location, and opportunity preferences
3. WHEN profile information is incomplete, THE Voice_Interface SHALL prompt the user for missing required fields
4. WHEN profile information is complete, THE Naavik_System SHALL store the profile with a unique identifier
5. WHEN profile storage succeeds, THE Voice_Interface SHALL confirm successful registration to the user
6. WHEN profile storage fails, THE Naavik_System SHALL retry storage up to three times before notifying the user of the error

### Requirement 2: Eligibility Checking

**User Story:** As a user, I want to know which opportunities I am eligible for, so that I can apply to relevant programs.

#### Acceptance Criteria

1. WHEN a user requests eligibility information, THE Eligibility_Engine SHALL compare the user profile against all active opportunities
2. WHEN the user matches eligibility criteria for an opportunity, THE Naavik_System SHALL include that opportunity in the response
3. WHEN the user does not match any active opportunities, THE Voice_Interface SHALL inform the user that no current matches exist
4. WHEN presenting eligible opportunities, THE Voice_Interface SHALL provide the opportunity name, deadline, and application method
5. WHEN multiple opportunities match, THE Eligibility_Engine SHALL rank them by application deadline with earliest deadlines first

### Requirement 3: Alert Registration

**User Story:** As a user, I want to register for alerts about future opportunities, so that I am notified when relevant programs are announced.

#### Acceptance Criteria

1. WHEN no current opportunities match the user profile, THE Voice_Interface SHALL offer to register the user for future alerts
2. WHEN the user accepts alert registration, THE Naavik_System SHALL add the user profile to the Alert_Registry
3. WHEN adding to the Alert_Registry, THE Naavik_System SHALL associate the user with their eligibility criteria
4. WHEN alert registration succeeds, THE Voice_Interface SHALL confirm the registration and explain the notification process
5. WHEN the user declines alert registration, THE Naavik_System SHALL end the call session without storing alert preferences

### Requirement 4: Opportunity Data Integration

**User Story:**
As a system administrator, I want opportunity data to be ingested into the system in a structured manner, so that users receive timely and accurate notifications.

### Acceptance Criteria

THE Naavik_System SHALL allow opportunity data to be ingested through configured data feeds or administrative input.

WHEN a new opportunity is added, THE Naavik_System SHALL store title, eligibility criteria, deadline, and application details.

WHEN opportunity data is incomplete, THE system SHALL reject the entry and log the issue.

WHEN opportunity data is successfully stored, THE system SHALL trigger eligibility matching workflows.

THE Naavik_System SHALL support event-driven updates through AWS EventBridge or equivalent event triggers.

### Requirement 5: Proactive User Notification

**User Story:** As a user, I want to receive phone calls when relevant opportunities are published, so that I do not miss application deadlines.

#### Acceptance Criteria

1. WHEN a new opportunity is stored, THE Eligibility_Engine SHALL identify all registered users who match the eligibility criteria
2. WHEN matching users are identified, THE Notification_Service SHALL queue outbound calls to those users
3. WHEN placing an outbound call, THE Voice_Interface SHALL attempt to reach the user up to three times over 24 hours
4. WHEN a user answers the call, THE Voice_Interface SHALL inform them about the matching opportunity including name, deadline, and how to apply
5. WHEN a user does not answer after three attempts, THE Naavik_System SHALL mark the notification as undelivered and log the outcome
6. WHEN a notification is successfully delivered, THE Naavik_System SHALL record the delivery timestamp and remove the user from the notification queue for that opportunity

### Requirement 6: Voice Interaction Accessibility

**User Story:** As a visually impaired user, I want clear voice-based interactions, so that I can use the system without visual aids.

#### Acceptance Criteria

1. THE Voice_Interface SHALL use clear speech synthesis with adjustable speaking rate
2. WHEN presenting information, THE Voice_Interface SHALL structure responses with clear verbal cues and pauses
3. WHEN collecting user input, THE Voice_Interface SHALL provide explicit instructions for voice or keypad responses
4. WHEN the user provides unclear input, THE Voice_Interface SHALL repeat the question with additional clarification
5. WHEN the user requests repetition, THE Voice_Interface SHALL repeat the last message without penalty

### Requirement 7: Data Privacy and Minimization

**User Story:** As a user, I want my personal data to be minimally collected and securely stored, so that my privacy is protected.

#### Acceptance Criteria

1. THE Naavik_System SHALL collect only the minimum data required for eligibility matching
2. THE Naavik_System SHALL encrypt all stored user profiles at rest using AES-256 encryption
3. THE Naavik_System SHALL encrypt all data in transit using TLS 1.2 or higher
4. WHEN a user requests profile deletion, THE Naavik_System SHALL remove all associated data within 24 hours
5. THE Naavik_System SHALL not share user data with third parties without explicit user consent

### Requirement 8: System Scalability and Reliability

**User Story:**
As a system administrator, I want the system to handle varying loads and remain available, so that users can access services reliably.

### Acceptance Criteria

THE Naavik_System SHALL use serverless AWS services to automatically scale based on demand.

WHEN call volume increases, THE system SHALL scale within AWS service limits.

WHEN a component fails, THE system SHALL retry the operation with controlled retry logic.

THE system SHALL log errors and operational events for monitoring and troubleshooting.

### Requirement 9: Multi-Language Support

**User Story:** As a user who speaks a regional language, I want to interact with the system in my preferred language, so that I can understand and use the service effectively.

#### Acceptance Criteria

1. WHEN a user calls the helpline, THE Voice_Interface SHALL prompt for language preference
2. THE Voice_Interface SHALL support at least Hindi and English for voice interactions
3. WHEN a language is selected, THE Voice_Interface SHALL conduct the entire call session in that language
4. WHEN the user requests a language change mid-call, THE Voice_Interface SHALL switch to the requested language
5. WHEN opportunity information is presented, THE Voice_Interface SHALL translate content to the user's selected language

### Requirement 10: Call Session Management

**User Story:** As a user, I want my call sessions to be efficient and resumable, so that I can complete interactions without frustration.

#### Acceptance Criteria

1. WHEN a call session begins, THE Naavik_System SHALL create a session identifier and maintain session state
2. WHEN a call is disconnected unexpectedly, THE Naavik_System SHALL preserve session state for 30 minutes
3. WHEN a user calls back within 30 minutes, THE Voice_Interface SHALL offer to resume the previous session
4. WHEN a call session exceeds 10 minutes, THE Voice_Interface SHALL summarize progress and offer to continue or schedule a callback
5. WHEN a call session completes successfully, THE Naavik_System SHALL log the session outcome and clear session state
