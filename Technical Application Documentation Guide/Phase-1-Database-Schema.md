# Phase 1.1: Database Schema Documentation - MagalGenis EHR

## 📊 **Complete Database ERD Visualization**

<lov-mermaid>
erDiagram
    %% Core User & Authentication Tables
    users {
        uuid id PK
        text first_name
        text last_name  
        text email
        uuid auth_user_id FK
        boolean is_active
        timestamptz created_at
        timestamptz updated_at
    }
    
    user_roles {
        uuid id PK
        uuid user_id FK
        user_role role
        boolean is_active
        timestamptz created_at
    }
    
    role_permissions {
        uuid id PK
        user_role role
        text permission_category
        text permission_action
        text resource_scope
        jsonb conditions
        boolean is_active
    }
    
    %% Client/Patient Management
    clients {
        uuid id PK "🔒 PHI"
        text first_name "🔒 PHI"
        text last_name "🔒 PHI"
        text middle_name "🔒 PHI"
        text email "🔒 PHI"
        date date_of_birth "🔒 PHI"
        text address_1 "🔒 PHI"
        text city "🔒 PHI"
        us_state state "🔒 PHI"
        text zip_code "🔒 PHI"
        uuid assigned_clinician_id FK
        uuid created_by FK
        boolean is_active
        timestamptz created_at
        timestamptz updated_at
    }
    
    client_phone_numbers {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        text phone_number "🔒 PHI"
        phone_type phone_type
        message_preference message_preference
        timestamptz created_at
    }
    
    client_emergency_contacts {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        text name "🔒 PHI"
        text phone_number "🔒 PHI"
        text email "🔒 PHI"
        text relationship "🔒 PHI"
        boolean is_primary
        timestamptz created_at
    }
    
    client_insurance {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        text insurance_company "🔒 PHI"
        text policy_number "🔒 PHI"
        text group_number "🔒 PHI"
        numeric copay_amount "🔒 PHI"
        boolean is_active
        timestamptz created_at
    }
    
    client_medications {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        text medication_name "🔒 PHI"
        text dosage "🔒 PHI"
        text frequency "🔒 PHI"
        text prescribing_doctor "🔒 PHI"
        boolean is_active
        timestamptz created_at
    }
    
    client_diagnoses {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        text diagnosis_code "🔒 PHI"
        text diagnosis_description "🔒 PHI"
        date diagnosed_date "🔒 PHI"
        boolean is_primary "🔒 PHI"
        text status
        timestamptz created_at
    }
    
    client_substance_history {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        substance_type substance_type "🔒 PHI"
        text amount "🔒 PHI"
        text frequency "🔒 PHI"
        boolean is_current "🔒 PHI"
        timestamptz created_at
    }
    
    client_treatment_history {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        treatment_type treatment_type "🔒 PHI"
        text provider_name "🔒 PHI"
        date start_date "🔒 PHI"
        date end_date "🔒 PHI"
        integer effectiveness_rating "🔒 PHI"
        timestamptz created_at
    }
    
    %% Scheduling & Appointments
    appointments {
        uuid id PK "⚠️ NO RLS"
        uuid client_id FK "🔒 PHI"
        uuid provider_id FK
        appointment_type appointment_type
        timestamptz start_time
        timestamptz end_time
        appointment_status status
        text title "🔒 PHI"
        text description "🔒 PHI"
        text location
        uuid created_by FK
        timestamptz created_at
    }
    
    appointment_reminders {
        uuid id PK "⚠️ NO RLS"
        uuid appointment_id FK
        text reminder_type
        integer send_before_minutes
        boolean is_sent
        timestamptz sent_at
        timestamptz created_at
    }
    
    appointment_conflicts {
        uuid id PK
        uuid appointment_id FK
        uuid conflicting_appointment_id FK
        text conflict_type
        timestamptz detected_at
        timestamptz resolved_at
        timestamptz created_at
    }
    
    appointment_waitlist {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        uuid provider_id FK
        appointment_type appointment_type
        date preferred_date "🔒 PHI"
        time preferred_time_start "🔒 PHI"
        integer priority
        boolean is_fulfilled
        timestamptz created_at
    }
    
    recurring_series {
        uuid id PK
        uuid created_by FK
        text pattern_type
        jsonb pattern_config
        date series_start
        date series_end
        boolean is_active
        timestamptz created_at
    }
    
    %% Clinical Documentation
    clinical_notes {
        uuid id PK "✅ RLS"
        uuid client_id FK "🔒 PHI"
        uuid provider_id FK
        note_type note_type
        text title "🔒 PHI"
        jsonb content "🔒 PHI"
        note_status status
        uuid signed_by FK
        timestamptz signed_at
        uuid co_signed_by FK
        timestamptz co_signed_at
        timestamptz created_at
    }
    
    note_versions {
        uuid id PK
        uuid note_id FK "🔒 PHI"
        integer version
        jsonb content "🔒 PHI"
        uuid created_by FK
        timestamptz created_at
    }
    
    note_reminders {
        uuid id PK
        uuid note_id FK
        uuid provider_id FK
        text reminder_type
        timestamptz due_date
        boolean is_dismissed
        timestamptz created_at
    }
    
    note_completion_tracking {
        uuid id PK
        uuid note_id FK
        uuid user_id FK
        timestamptz started_at
        timestamptz completed_at
        integer completion_percentage
        integer time_spent_minutes
        timestamptz created_at
    }
    
    %% Treatment Planning
    treatment_goals {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        uuid provider_id FK
        text goal_text "🔒 PHI"
        text target_date "🔒 PHI"
        text status
        integer priority
        timestamptz created_at
    }
    
    goal_objectives {
        uuid id PK
        uuid goal_id FK "🔒 PHI"
        text objective_text "🔒 PHI"
        boolean is_completed
        date completed_date
        timestamptz created_at
    }
    
    %% Billing & Financial
    payers {
        uuid id PK
        text name
        payer_type payer_type
        text electronic_payer_id
        text contact_email
        text phone_number
        boolean is_active
        timestamptz created_at
    }
    
    payer_contracts {
        uuid id PK
        uuid payer_id FK
        text contract_name
        text contract_number
        date effective_date
        date expiration_date
        numeric reimbursement_rate
        contract_status status
        timestamptz created_at
    }
    
    payer_fee_schedules {
        uuid id PK
        uuid payer_id FK
        text cpt_code
        numeric fee_amount
        date effective_date
        date expiration_date
        boolean is_active
        timestamptz created_at
    }
    
    claims {
        uuid id PK "✅ RLS"
        uuid client_id FK "🔒 PHI"
        uuid provider_id FK
        uuid payer_id FK
        text claim_number
        date service_date
        numeric total_amount
        claim_status status
        date submission_date
        text authorization_number "🔒 PHI"
        text[] diagnosis_codes "🔒 PHI"
        timestamptz created_at
    }
    
    claim_line_items {
        uuid id PK
        uuid claim_id FK "🔒 PHI"
        text cpt_code "🔒 PHI"
        date service_date "🔒 PHI"
        numeric charge_amount
        numeric allowed_amount
        numeric paid_amount
        integer units
        timestamptz created_at
    }
    
    payments {
        uuid id PK "✅ RLS"
        uuid client_id FK "🔒 PHI"
        uuid claim_id FK "🔒 PHI"
        uuid payer_id FK
        text payment_number
        date payment_date
        numeric payment_amount
        payment_method payment_method
        payment_status status
        text reference_number
        uuid processed_by FK
        timestamptz created_at
    }
    
    payment_allocations {
        uuid id PK
        uuid payment_id FK
        uuid claim_line_item_id FK
        numeric allocated_amount
        text allocation_type
        timestamptz created_at
    }
    
    patient_statements {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        text statement_number
        date statement_date
        numeric total_amount
        numeric current_balance
        date due_date
        text status
        uuid created_by FK
        timestamptz created_at
    }
    
    insurance_verifications {
        uuid id PK
        uuid client_id FK "🔒 PHI"
        uuid insurance_id FK "🔒 PHI"
        date verification_date
        verification_status status
        boolean benefits_verified
        numeric copay_amount "🔒 PHI"
        uuid verified_by FK
        timestamptz created_at
    }
    
    %% Messaging & Communication
    conversations {
        uuid id PK "✅ RLS"
        uuid client_id FK "🔒 PHI"
        uuid therapist_id FK
        uuid created_by FK
        text title "🔒 PHI"
        text category
        text priority
        text status
        timestamptz last_message_at
        timestamptz created_at
    }
    
    messages {
        uuid id PK "✅ RLS"
        uuid conversation_id FK "🔒 PHI"
        uuid sender_id FK
        text content "🔒 PHI"
        text message_type
        text priority
        boolean is_read
        timestamptz read_at
        timestamptz created_at
    }
    
    message_recipients {
        uuid id PK
        uuid message_id FK
        uuid recipient_id FK
        boolean is_read
        timestamptz read_at
        timestamptz created_at
    }
    
    %% Staff Management
    staff_profiles {
        uuid id PK
        uuid user_id FK
        text employee_id
        text job_title
        text department
        text phone_number
        text npi_number
        text license_number
        text license_state
        date license_expiry_date
        date hire_date
        numeric billing_rate
        user_status status
        timestamptz created_at
    }
    
    certifications {
        uuid id PK "✅ RLS"
        uuid user_id FK
        text certification_name
        text certification_number
        text issuing_organization
        date issue_date
        date expiry_date
        text status
        integer renewal_period_months
        timestamptz created_at
    }
    
    supervision_relationships {
        uuid id PK
        uuid supervisor_id FK
        uuid supervisee_id FK
        supervision_requirement_type supervision_type
        date start_date
        date end_date
        boolean is_active
        timestamptz created_at
    }
    
    %% Compliance & Audit
    compliance_deadlines {
        uuid id PK
        uuid provider_id FK
        text deadline_type
        timestamptz deadline_date
        boolean is_met
        integer notes_completed
        integer notes_pending
        boolean reminder_sent_24h
        timestamptz created_at
    }
    
    compliance_metrics {
        uuid id PK
        uuid user_id FK
        text metric_type
        numeric metric_value
        date period_start
        date period_end
        timestamptz created_at
    }
    
    compliance_reports {
        uuid id PK "✅ RLS"
        uuid generated_by FK
        text report_type
        date period_start
        date period_end
        jsonb report_data
        text status
        timestamptz created_at
    }
    
    deadline_exception_requests {
        uuid id PK
        uuid provider_id FK
        uuid session_completion_id FK
        text reason
        timestamptz requested_extension_until
        approval_status status
        uuid reviewed_by FK
        timestamptz created_at
    }
    
    audit_logs {
        uuid id PK "✅ RLS"
        uuid user_id FK
        text action
        text resource_type
        uuid resource_id
        jsonb details
        inet ip_address
        text user_agent
        timestamptz created_at
    }
    
    hipaa_access_logs {
        uuid id PK "✅ RLS"
        uuid user_id FK
        uuid patient_id FK "🔒 PHI"
        text access_type
        text data_accessed "🔒 PHI"
        text purpose
        boolean authorized
        inet ip_address
        timestamptz created_at
    }
    
    security_audit_logs {
        uuid id PK
        uuid user_id FK
        text event_type
        text resource_type
        text action
        jsonb event_details
        text severity
        text status
        inet ip_address
        timestamptz created_at
    }
    
    %% System & Configuration
    practice_settings {
        uuid id PK
        text setting_category
        text setting_key
        jsonb setting_value
        text description
        boolean is_encrypted
        timestamptz created_at
    }
    
    api_logs {
        uuid id PK "✅ RLS"
        uuid user_id FK
        text method
        text url
        integer status_code
        jsonb request_body
        jsonb response_body
        integer response_time_ms
        inet ip_address
        timestamptz created_at
    }
    
    data_classifications {
        uuid id PK "✅ RLS"
        text table_name
        text column_name
        text classification
        boolean encryption_required
        integer retention_period_days
        jsonb anonymization_rules
        timestamptz created_at
    }
    
    %% Billing Code References
    cpt_codes {
        uuid id PK "✅ RLS"
        text code
        text description
        text category
        boolean is_active
        timestamptz created_at
    }
    
    diagnosis_codes {
        uuid id PK "✅ RLS"
        text code
        text description
        text category
        boolean is_active
        timestamptz created_at
    }
    
    %% Reporting & Analytics
    aggregated_metrics {
        uuid id PK "✅ RLS"
        uuid provider_id FK
        uuid client_id FK
        text metric_type
        text period_type
        date period_start
        date period_end
        jsonb metrics
        timestamptz created_at
    }
    
    report_cache {
        uuid id PK
        text cache_key
        text report_type
        jsonb data
        timestamptz expires_at
        timestamptz created_at
    }
    
    report_audit_logs {
        uuid id PK
        uuid user_id FK
        text report_type
        text action
        jsonb filters
        integer execution_time_ms
        timestamptz created_at
    }
    
    query_cache {
        uuid id PK
        text cache_key
        jsonb cache_data
        timestamptz expires_at
        timestamptz created_at
    }
    
    %% Provider Compensation
    provider_compensation_config {
        uuid id PK
        uuid provider_id FK
        compensation_type compensation_type
        numeric base_session_rate
        numeric hourly_rate
        date effective_date
        boolean is_active
        timestamptz created_at
    }
    
    session_rate_multipliers {
        uuid id PK
        uuid provider_id FK
        session_type session_type
        integer duration_minutes
        numeric multiplier
        boolean is_active
        timestamptz created_at
    }
    
    session_completions {
        uuid id PK
        uuid provider_id FK
        uuid client_id FK "🔒 PHI"
        uuid appointment_id FK
        session_type session_type
        date session_date
        integer duration_minutes
        numeric calculated_payment
        text completion_notes "🔒 PHI"
        timestamptz created_at
    }
    
    payment_calculations {
        uuid id PK "✅ RLS"
        uuid user_id FK
        compensation_type compensation_type
        date pay_period_start
        date pay_period_end
        numeric gross_amount
        numeric net_amount
        integer total_sessions
        jsonb calculation_details
        payment_status status
        uuid processed_by FK
        timestamptz created_at
    }
    
    productivity_goals {
        uuid id PK
        uuid user_id FK
        text goal_type
        date date
        numeric target_value
        numeric current_value
        boolean is_met
        timestamptz created_at
    }
    
    %% Rate Limiting & Performance
    rate_limits {
        uuid id PK
        text identifier
        integer request_count
        timestamptz created_at
        timestamptz updated_at
    }
    
    performance_metrics {
        uuid id PK
        text metric_name
        numeric value
        text unit
        jsonb metadata
        timestamptz recorded_at
        timestamptz created_at
    }
    
    %% Patient Access Control
    patient_access_permissions {
        uuid id PK "✅ RLS"
        uuid client_id FK "🔒 PHI"
        uuid user_id FK
        uuid granted_by FK
        text access_type
        timestamptz granted_at
        timestamptz revoked_at
        uuid revoked_by FK
        boolean is_active
        text notes
    }
    
    %% Relationships - Core Authentication
    users ||--o{ user_roles : "has roles"
    user_roles }o--|| role_permissions : "defines permissions"
    
    %% Relationships - Client Management
    users ||--o{ clients : "assigned_clinician"
    users ||--o{ clients : "created_by"
    clients ||--o{ client_phone_numbers : "has phones"
    clients ||--o{ client_emergency_contacts : "has contacts"
    clients ||--o{ client_insurance : "has insurance"
    clients ||--o{ client_medications : "has medications"
    clients ||--o{ client_diagnoses : "has diagnoses"
    clients ||--o{ client_substance_history : "has substance history"
    clients ||--o{ client_treatment_history : "has treatment history"
    
    %% Relationships - Scheduling
    clients ||--o{ appointments : "schedules"
    users ||--o{ appointments : "provider"
    users ||--o{ appointments : "created_by"
    appointments ||--o{ appointment_reminders : "has reminders"
    appointments ||--o{ appointment_conflicts : "primary conflict"
    appointments ||--o{ appointment_conflicts : "conflicting appointment"
    clients ||--o{ appointment_waitlist : "waitlisted"
    users ||--o{ appointment_waitlist : "provider"
    users ||--o{ recurring_series : "created_by"
    recurring_series ||--o{ appointments : "generates"
    
    %% Relationships - Clinical Documentation
    clients ||--o{ clinical_notes : "subject of"
    users ||--o{ clinical_notes : "provider"
    users ||--o{ clinical_notes : "signed_by"
    users ||--o{ clinical_notes : "co_signed_by"
    clinical_notes ||--o{ note_versions : "versioned"
    clinical_notes ||--o{ note_reminders : "reminds about"
    clinical_notes ||--o{ note_completion_tracking : "tracks completion"
    users ||--o{ note_versions : "created_by"
    users ||--o{ note_reminders : "assigned to"
    users ||--o{ note_completion_tracking : "tracks for"
    
    %% Relationships - Treatment Planning
    clients ||--o{ treatment_goals : "has goals"
    users ||--o{ treatment_goals : "provider"
    treatment_goals ||--o{ goal_objectives : "has objectives"
    
    %% Relationships - Billing
    payers ||--o{ payer_contracts : "has contracts"
    payers ||--o{ payer_fee_schedules : "has fee schedules"
    clients ||--o{ claims : "subject of"
    users ||--o{ claims : "provider"
    payers ||--o{ claims : "payer"
    claims ||--o{ claim_line_items : "contains"
    clients ||--o{ payments : "for"
    claims ||--o{ payments : "pays for"
    payers ||--o{ payments : "from"
    users ||--o{ payments : "processed_by"
    payments ||--o{ payment_allocations : "allocated"
    claim_line_items ||--o{ payment_allocations : "receives allocation"
    clients ||--o{ patient_statements : "receives"
    users ||--o{ patient_statements : "created_by"
    clients ||--o{ insurance_verifications : "verified for"
    client_insurance ||--o{ insurance_verifications : "verifies"
    users ||--o{ insurance_verifications : "verified_by"
    
    %% Relationships - Communication
    clients ||--o{ conversations : "participates in"
    users ||--o{ conversations : "therapist"
    users ||--o{ conversations : "created_by"
    conversations ||--o{ messages : "contains"
    users ||--o{ messages : "sender"
    messages ||--o{ message_recipients : "sent to"
    users ||--o{ message_recipients : "recipient"
    
    %% Relationships - Staff Management
    users ||--|| staff_profiles : "has profile"
    users ||--o{ certifications : "holds"
    users ||--o{ supervision_relationships : "supervisor"
    users ||--o{ supervision_relationships : "supervisee"
    
    %% Relationships - Compliance
    users ||--o{ compliance_deadlines : "has deadlines"
    users ||--o{ compliance_metrics : "measured for"
    users ||--o{ compliance_reports : "generated_by"
    users ||--o{ deadline_exception_requests : "provider"
    users ||--o{ deadline_exception_requests : "reviewed_by"
    session_completions ||--o{ deadline_exception_requests : "related to"
    
    %% Relationships - Audit & Security
    users ||--o{ audit_logs : "performed action"
    users ||--o{ hipaa_access_logs : "accessed by"
    clients ||--o{ hipaa_access_logs : "patient accessed"
    users ||--o{ security_audit_logs : "security event"
    users ||--o{ api_logs : "made request"
    
    %% Relationships - Analytics & Reporting
    users ||--o{ aggregated_metrics : "provider metrics"
    clients ||--o{ aggregated_metrics : "client metrics"
    users ||--o{ report_audit_logs : "ran report"
    
    %% Relationships - Compensation
    users ||--o{ provider_compensation_config : "compensation config"
    users ||--o{ session_rate_multipliers : "rate multipliers"
    users ||--o{ session_completions : "completed sessions"
    clients ||--o{ session_completions : "session for"
    appointments ||--o{ session_completions : "documented in"
    users ||--o{ payment_calculations : "calculated for"
    users ||--o{ payment_calculations : "processed_by"
    users ||--o{ productivity_goals : "has goals"
    
    %% Relationships - Patient Access
    clients ||--o{ patient_access_permissions : "access to"
    users ||--o{ patient_access_permissions : "granted to"
    users ||--o{ patient_access_permissions : "granted_by"
    users ||--o{ patient_access_permissions : "revoked_by"
</lov-mermaid>
    payers ||--o{ claims : "payer"
    
    %% Compliance & Audit
    users ||--o{ audit_logs : "performed_action"
    users ||--o{ hipaa_access_logs : "accessed"
    users ||--o{ compliance_deadlines : "has"
    users ||--o{ deadline_exception_requests : "requested"
    
    %% Practice Management
    users ||--o{ patient_access_permissions : "granted_to"
    clients ||--o{ patient_access_permissions : "access_to"
    
    %% Performance & Analytics
    users ||--o{ performance_metrics : "generated_by"
    users ||--o{ aggregated_metrics : "provider"
    clients ||--o{ aggregated_metrics : "client"
    
    %% System & Configuration
    users ||--o{ api_logs : "user_action"
    users ||--o{ rate_limits : "rate_limited"
    
    %% Core Entity Definitions
    users {
        uuid id PK "🔑 Primary Key"
        uuid auth_user_id FK "🔗 Supabase Auth"
        text first_name "✅ PHI"
        text last_name "✅ PHI" 
        text email UK "✅ PHI"
        boolean is_active "Default: true"
        timestamp created_at "Auto"
        timestamp updated_at "Auto"
    }
    
    user_roles {
        uuid id PK
        uuid user_id FK
        user_role role "ENUM: Admin, Clinician, etc"
        boolean is_active "Default: true"
        timestamp created_at
    }
    
    clients {
        uuid id PK "🔑 Patient ID"
        text first_name "✅ PHI"
        text last_name "✅ PHI"
        date date_of_birth "✅ PHI"
        text email "✅ PHI"
        uuid assigned_clinician_id FK
        boolean is_active "Default: true"
        boolean hipaa_signed "Default: false"
        timestamp created_at
        timestamp updated_at
    }
    
    clinical_notes {
        uuid id PK
        uuid provider_id FK
        uuid client_id FK  
        text title "✅ PHI"
        jsonb content "✅ PHI - Clinical Data"
        note_type note_type "ENUM"
        note_status status "Default: draft"
        timestamp signed_at
        uuid signed_by FK
        timestamp created_at
        timestamp updated_at
    }
    
    appointments {
        uuid id PK
        uuid provider_id FK
        uuid client_id FK
        timestamp start_time "✅ PHI"
        timestamp end_time "✅ PHI"
        appointment_type appointment_type "ENUM"
        appointment_status status "Default: scheduled"
        text notes "✅ PHI"
        boolean is_recurring "Default: false"
        timestamp created_at
    }
    
    appointment_reminders {
        uuid id PK
        uuid appointment_id FK
        text reminder_type
        integer send_before_minutes
        boolean is_sent "Default: false"
        timestamp sent_at
        timestamp created_at
    }
    
    conversations {
        uuid id PK
        uuid therapist_id FK
        uuid client_id FK
        text title "✅ PHI"
        text status "Default: active"
        timestamp last_message_at
        timestamp created_at
        timestamp updated_at
    }
    
    messages {
        uuid id PK
        uuid conversation_id FK
        uuid sender_id FK
        text content "✅ PHI"
        boolean is_read "Default: false"
        timestamp read_at
        timestamp created_at
    }
    
    claims {
        uuid id PK
        uuid provider_id FK
        uuid client_id FK
        uuid payer_id FK
        text claim_number "✅ PHI"
        date service_date "✅ PHI"
        numeric total_amount "✅ PHI"
        claim_status status "Default: draft"
        timestamp created_at
    }
    
    hipaa_access_logs {
        uuid id PK
        uuid user_id FK
        uuid patient_id "Patient accessed"
        text access_type
        text data_accessed
        boolean authorized "Default: true"
        inet ip_address
        timestamp created_at
    }
    
    audit_logs {
        uuid id PK
        uuid user_id FK
        text action
        text resource_type  
        uuid resource_id
        jsonb details
        inet ip_address
        timestamp created_at
    }
    
    client_insurance {
        uuid id PK
        uuid client_id FK
        text insurance_company "✅ PHI"
        text policy_number "✅ PHI"
        text group_number "✅ PHI"
        boolean is_active "Default: true"
        timestamp created_at
    }
    
    performance_metrics {
        uuid id PK
        uuid user_id FK
        text metric_name
        numeric metric_value
        text metric_unit "Default: ms"
        timestamp created_at
    }
    
    query_cache {
        uuid id PK
        text cache_key UK
        jsonb cache_data
        timestamp expires_at
        timestamp created_at
    }
    
    rate_limits {
        uuid id PK  
        text identifier UK
        integer request_count "Default: 1"
        timestamp created_at
        timestamp updated_at
    }
    
    api_logs {
        uuid id PK
        text method
        text url
        integer status_code
        integer response_time_ms
        uuid user_id FK
        inet ip_address
        jsonb request_body
        jsonb response_body
        timestamp created_at
    }
```

## 🚨 Critical Security Status Legend

**Table Security Status:**
- 🟢 **Protected** (Has RLS policies)
- 🔴 **UNPROTECTED** (Missing RLS - CRITICAL)
- 🟡 **Partial** (Some protection, needs review)

### Security Status by Module:

**🟢 PROTECTED TABLES:**
- `users`, `user_roles`, `staff_profiles`
- `clients`, `client_insurance`, `client_diagnoses`
- `clinical_notes`, `note_versions`
- `conversations`, `messages`
- `claims`, `claim_line_items`
- `hipaa_access_logs`, `audit_logs`

**🔴 UNPROTECTED TABLES (IMMEDIATE ACTION REQUIRED):**
- `appointments` ⚠️ **Contains PHI - appointment times**
- `appointment_reminders` ⚠️ **Linked to PHI data**

**🟡 SYSTEM TABLES (Minimal Risk):**
- `query_cache`, `rate_limits`, `api_logs`
- `performance_metrics`

## Complete Table Documentation

### 🔐 Core User & Authentication Tables

#### `users` (Core Identity Table)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Internal user ID |
| auth_user_id | uuid | FK to auth.users | ❌ | Supabase auth reference |
| first_name | text | NOT NULL | ✅ | User's first name |
| last_name | text | NOT NULL | ✅ | User's last name |
| email | text | UNIQUE, NOT NULL | ✅ | User's email address |
| is_active | boolean | default true | ❌ | Account status |
| created_at | timestamp | default now() | ❌ | Account creation time |
| updated_at | timestamp | default now() | ❌ | Last update time |

**RLS Status:** ✅ Fully Protected  
**Relationships:** Core table - related to all user-specific data

#### `user_roles` (RBAC Implementation)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Role assignment ID |
| user_id | uuid | FK to users, NOT NULL | ❌ | User reference |
| role | user_role | ENUM, NOT NULL | ❌ | Role type |
| is_active | boolean | default true | ❌ | Role status |
| created_at | timestamp | default now() | ❌ | Assignment date |

**Available Roles:**
- Practice Administrator
- Clinical Administrator  
- Clinician
- Support Staff
- Billing Staff
- Compliance Officer

**RLS Status:** ✅ Protected - Users can view own roles, admins can manage all

### 👥 Client/Patient Management Tables

#### `clients` (Patient Master Record)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Patient ID |
| first_name | text | NOT NULL | ✅ | Patient first name |
| last_name | text | NOT NULL | ✅ | Patient last name |
| date_of_birth | date | | ✅ | Patient DOB |
| email | text | | ✅ | Patient email |
| assigned_clinician_id | uuid | FK to users | ❌ | Primary clinician |
| is_active | boolean | default true | ❌ | Patient status |
| hipaa_signed | boolean | default false | ❌ | HIPAA authorization |
| created_at | timestamp | default now() | ❌ | Registration date |

**RLS Status:** ✅ Protected - Role-based access with clinician assignments

#### `client_insurance` (Insurance Information)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Insurance record ID |
| client_id | uuid | FK to clients | ❌ | Patient reference |
| insurance_company | text | | ✅ | Insurance provider |
| policy_number | text | | ✅ | Policy number |
| group_number | text | | ✅ | Group number |
| is_active | boolean | default true | ❌ | Coverage status |

**RLS Status:** ✅ Protected - Linked to client access permissions

### 📋 Clinical Documentation Tables

#### `clinical_notes` (Clinical Documentation)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Note ID |
| provider_id | uuid | FK to users | ❌ | Authoring provider |
| client_id | uuid | FK to clients | ❌ | Patient reference |
| title | text | NOT NULL | ✅ | Note title |
| content | jsonb | NOT NULL | ✅ | Note content |
| note_type | note_type | ENUM | ❌ | Type of note |
| status | note_status | ENUM, default 'draft' | ❌ | Note status |
| signed_at | timestamp | | ❌ | Signature timestamp |
| signed_by | uuid | FK to users | ❌ | Signing provider |

**Note Types:** therapy_session, intake_assessment, treatment_plan, progress_note, discharge_summary  
**RLS Status:** ✅ Protected - Provider and assigned clinician access

### 📅 Scheduling Tables

#### `appointments` (Appointment Management)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Appointment ID |
| provider_id | uuid | FK to users | ❌ | Provider reference |
| client_id | uuid | FK to clients | ❌ | Patient reference |
| start_time | timestamp | NOT NULL | ✅ | Appointment start |
| end_time | timestamp | NOT NULL | ✅ | Appointment end |
| appointment_type | appointment_type | ENUM | ❌ | Type of appointment |
| status | appointment_status | ENUM, default 'scheduled' | ❌ | Appointment status |

**⚠️ RLS Status:** ❌ **CRITICAL - NO RLS POLICIES FOUND**

#### `appointment_reminders` (Reminder System)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Reminder ID |
| appointment_id | uuid | FK to appointments, NOT NULL | ❌ | Appointment reference |
| reminder_type | text | NOT NULL | ❌ | Type of reminder |
| send_before_minutes | integer | NOT NULL | ❌ | Lead time in minutes |
| is_sent | boolean | default false | ❌ | Delivery status |

**⚠️ RLS Status:** ❌ **CRITICAL - NO RLS POLICIES FOUND**

### 💬 Messaging Tables

#### `conversations` (Message Threads)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Conversation ID |
| therapist_id | uuid | FK to users, NOT NULL | ❌ | Therapist reference |
| client_id | uuid | FK to clients, NOT NULL | ❌ | Client reference |
| title | text | | ✅ | Conversation subject |
| status | text | default 'active' | ❌ | Conversation status |

**RLS Status:** ✅ Protected - Therapist and assigned clinician access

#### `messages` (Individual Messages)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Message ID |
| conversation_id | uuid | FK to conversations, NOT NULL | ❌ | Thread reference |
| sender_id | uuid | FK to users, NOT NULL | ❌ | Message sender |
| content | text | NOT NULL | ✅ | Message content |
| is_read | boolean | default false | ❌ | Read status |

**RLS Status:** ✅ Protected - Conversation participants only

### 💰 Billing & Claims Tables

#### `claims` (Insurance Claims)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Claim ID |
| provider_id | uuid | FK to users | ❌ | Billing provider |
| client_id | uuid | FK to clients | ❌ | Patient reference |
| claim_number | text | NOT NULL | ✅ | Claim identifier |
| service_date | date | NOT NULL | ✅ | Date of service |
| total_amount | numeric | NOT NULL | ✅ | Claim amount |
| status | claim_status | ENUM, default 'draft' | ❌ | Claim status |

**RLS Status:** ✅ Protected - Basic access controls in place

### 🔍 Compliance & Audit Tables

#### `hipaa_access_logs` (PHI Access Tracking)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Log entry ID |
| user_id | uuid | FK to users, NOT NULL | ❌ | Accessing user |
| patient_id | uuid | NOT NULL | ❌ | Patient accessed |
| access_type | text | NOT NULL | ❌ | Type of access |
| data_accessed | text | | ❌ | Specific data viewed |
| authorized | boolean | default true | ❌ | Access authorization |

**RLS Status:** ✅ Protected - Admins and own access logs only

#### `audit_logs` (General System Audit)
| Column | Type | Constraints | PHI | Description |
|--------|------|-------------|-----|-------------|
| id | uuid | PK, default gen_random_uuid() | ❌ | Log entry ID |
| user_id | uuid | FK to users | ❌ | User performing action |
| action | text | NOT NULL | ❌ | Action performed |
| resource_type | text | NOT NULL | ❌ | Resource affected |
| resource_id | uuid | | ❌ | Specific resource |
| details | jsonb | | ❌ | Additional details |

**RLS Status:** ✅ Protected - Administrator access only

## Critical Security Findings

### 🚨 Immediate Action Required

1. **Missing RLS Policies:**
   - `appointments` table has NO RLS policies
   - `appointment_reminders` table has NO RLS policies
   - These tables contain PHI (appointment times, patient references)

2. **Recommended RLS Policies for Appointments:**
   ```sql
   -- Allow providers to manage their own appointments
   CREATE POLICY "Providers can manage own appointments"
   ON appointments FOR ALL
   USING (provider_id = get_current_user_internal_id());
   
   -- Allow assigned clinicians to view client appointments
   CREATE POLICY "Clinicians can view assigned client appointments"
   ON appointments FOR SELECT
   USING (client_id IN (
     SELECT id FROM clients 
     WHERE assigned_clinician_id = get_current_user_internal_id()
   ));
   ```

### ✅ Security Strengths

1. **Comprehensive User Management:** Proper RBAC implementation
2. **PHI Protection:** Most clinical tables have appropriate RLS
3. **Audit Trails:** HIPAA access logging implemented
4. **Data Isolation:** Client data properly scoped to assigned clinicians

## PHI Classification Summary

### High PHI Risk Tables:
- `clients` (demographics, contact info)
- `clinical_notes` (clinical content)
- `appointments` (scheduling data)
- `messages` (communication content)
- `claims` (billing information)

### Low PHI Risk Tables:
- `users` (staff information)
- `user_roles` (permissions)
- `audit_logs` (system events)
- `query_cache` (performance data)

## Database Functions & Triggers

### Security Functions:
- `get_current_user_internal_id()` - Get internal user ID from auth
- `has_role()` - Check user permissions
- `can_access_patient()` - Verify patient access rights
- `log_hipaa_access()` - Record PHI access

### Audit Functions:
- `update_updated_at_column()` - Automatic timestamp updates
- `detect_appointment_conflicts()` - Scheduling conflict detection

## Next Steps

1. **Immediate:** Implement RLS policies for scheduling tables
2. **Phase 1.2:** Complete security and HIPAA compliance mapping
3. **Phase 1.3:** Document complete RBAC matrix
4. **Phase 2:** Begin module-by-module functional documentation

---
**Last Updated:** January 16, 2025  
**Status:** Phase 1.1 Complete  
**Next Phase:** 1.2 Security & HIPAA Compliance Mapping