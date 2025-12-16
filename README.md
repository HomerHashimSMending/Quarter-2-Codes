// --- 1. START & INPUT ---

START
    
    // Initialize data structures
    DEFINE Student_List AS List of Student_Records
    DEFINE Student_Averages AS List of Real
    
    // Get data for the first student (S1)
    CALL GET_STUDENT_DATA (S1_Name, S1_ID, S1_Grades_Map)
    ADD (S1_Name, S1_ID, S1_Grades_Map) TO Student_List
    
    // Check for multiple students
    PROMPT "Do you want to process multiple students? (Yes/No)"
    INPUT Multi_Student_Flag
    
    IF Multi_Student_Flag IS "Yes" THEN
        WHILE "More Students to input?" DO
            CALL GET_STUDENT_DATA (Si_Name, Si_ID, Si_Grades_Map)
            ADD (Si_Name, Si_ID, Si_Grades_Map) TO Student_List
        END WHILE
    END IF

// --- 2. INDIVIDUAL STUDENT PROCESSING (Loop through all input students) ---

FOR EACH Student IN Student_List DO
    
    // Initialize lists for the current student
    DEFINE Subject_Remarks_List AS List of (Subject, Grade, Remark)
    DEFINE List_2_5_Better AS List of Subject
    DEFINE List_2_75_Lower AS List of Subject
    DEFINE Sum_Grades AS Real = 0
    DEFINE Subject_Count AS Integer = 0
    
    // Process each subject's grade
    FOR EACH Subject, Grade IN Student.Grades_Map DO
        
        // a. Remark Generation
        SET Remark = CALL ASSIGN_REMARK (Grade)
        ADD (Subject, Grade, Remark) TO Subject_Remarks_List
        
        // b. List 2.5 or Better
        IF Grade <= 2.5 THEN
            ADD Subject TO List_2_5_Better
        END IF
        
        // c. List 2.75 or Lower
        IF Grade <= 2.75 THEN
            ADD Subject TO List_2_75_Lower
        END IF
        
        // Prepare for average calculation
        SET Sum_Grades = Sum_Grades + Grade
        SET Subject_Count = Subject_Count + 1
        
    END FOR
    
    // d. Compute General Average (Avg_i)
    SET Avg_i = Sum_Grades / Subject_Count
    ADD Avg_i TO Student_Averages
    
    // e. Output Individual Results
    DISPLAY "--- Results for " + Student.Name + " (ID: " + Student.ID + ") ---"
    DISPLAY Subject_Remarks_List
    DISPLAY "Subjects scored 2.5 or better: " + List_2_5_Better
    DISPLAY "Subjects scored 2.75 or lower: " + List_2_75_Lower
    DISPLAY "General Average: " + Avg_i
    
END FOR

// --- 3. OUTPUT & MULTI-STUDENT PROCESSING ---

IF Count(Student_List) > 1 THEN
    
    // Compute Combined General Average (Avg_Combined)
    SET Sum_Averages = SUM(Student_Averages)
    SET Student_Count = Count(Student_List)
    SET Avg_Combined = Sum_Averages / Student_Count
    
    // Output Combined Results
    DISPLAY "-----------------------------------"
    DISPLAY "Combined General Average for all " + Student_Count + " students: " + Avg_Combined
    
END IF

END

// --- Subroutine for Grade-to-Remark Mapping ---
FUNCTION ASSIGN_REMARK (Grade) RETURNS Remark
    IF Grade <= 1.25 THEN
        RETURN "Excellent"
    ELSE IF Grade <= 1.75 THEN
        RETURN "Very Good"
    ELSE IF Grade <= 2.25 THEN
        RETURN "Good"
    ELSE IF Grade <= 2.5 THEN
        RETURN "Satisfactory"
    ELSE IF Grade <= 3.0 THEN
        RETURN "Passing"
    ELSE
        RETURN "Failed/Conditional"
    END IF
END FUNCTION

// --- Subroutine for Data Input (Placeholder) ---
FUNCTION GET_STUDENT_DATA (OUT Name, OUT ID, OUT Grades_Map)
    // Code to prompt user for Name, ID, and all Subject Grades
END FUNCTION
