#1. Show all of the patients grouped into weight groups.
- Show the total amount of patients in each weight group.
- Order the list by the weight group decending.
- For example, if they weight 100 to 109 they are placed in the 100 weight group, 110-119 = 110 weight group, etc.
```sql
SELECT
  COUNT(*) AS patients_in_group,
  FLOOR(weight / 10) * 10 AS weight_group
FROM patients
GROUP BY weight_group
ORDER BY weight_group DESC;
```
#2. Show patient_id, weight, height, isObese from the patients table. Display isObese as a boolean 0 or 1. Obese is defined as weight(kg)/(height(m)2) >= 30. weight is in units kg. height is in units cm.
```sql
SELECT 
  patient_id,
  weight,
  height,
  CASE WHEN (weight/SQUARE(height/100.0)) >= 30 THEN 1 ELSE 0 END as isObese
FROM patients
```
#3. Show patient_id, first_name, last_name, and attending doctor's specialty. Show only the patients who has a diagnosis as 'Epilepsy' and the doctor's first name is 'Lisa'.
```sql
SELECT 
  patients.patient_id,
  patients.first_name, 
  patients.last_name, 
  doctors.specialty
FROM patients
JOIN admissions
ON patients.patient_id = admissions.patient_id
JOIN doctors
ON admissions.attending_doctor_id = doctors.doctor_id
WHERE admissions.diagnosis = 'Epilepsy' AND doctors.first_name = 'Lisa'
```
#4. All patients who have gone through admissions, can see their medical documents on our site. Those patients are given a temporary password after their first admission. Show the patient_id and temp_password. The password must be the following, in order:
1. patient_id
2. the numerical length of patient's last_name
3. year of patient's birth_date
```sql
SELECT DISTINCT
  patients.patient_id, 
  concat(patients.patient_id, LEN(last_name), YEAR(birth_date)) AS temp_password
FROM patients
JOIN admissions
ON patients.patient_id = admissions.patient_id
WHERE admission_date IS NOT NULL
```
#5. Each admission costs $50 for patients without insurance, and $10 for patients with insurance. 
- All patients with an even patient_id have insurance.
- Give each patient a 'Yes' if they have insurance, and a 'No' if they don't have insurance.
- Add up the admission_total cost for each has_insurance group.
```sql
SELECT 
  CASE WHEN (patient_id % 2 = 0) THEN 'Yes' ELSE 'No' END AS has_insurance,
  SUM (CASE WHEN patient_id % 2 = 0 THEN 10 ELSE 50 END) AS total_cost
FROM admissions
GROUP BY has_insurance
```
#6. We are looking for a specific patient. Pull all columns for the patient who matches the following criteria:
- First_name contains an 'r' after the first two letters.
- Identifies their gender as 'F'
- Born in February, May, or December
- Their weight would be between 60kg and 80kg
- Their patient_id is an odd number
- They are from the city 'Kingston.
```sql
SELECT * FROM patients
WHERE 
  first_name LIKE '__r%' 
  AND gender = 'F'
  AND MONTH(birth_date) IN (12, 02, 05)
  AND weight between 60 AND 80
  AND patient_id % 2 <> 0 
  AND city = 'Kingston'
```
#7. Show the percent of patients that have 'M' as their gender. Round the answer to the nearest hundreth number and in percent form.
```sql
SELECT CONCAT(ROUND(SUM(gender='M') / CAST(COUNT(*) AS float), 4) * 100, '%')
FROM patients;
```
#8. Sort the province names in ascending order in such a way that the province 'Ontario' is always on top.
```sql
SELECT province_name
FROM province_names
ORDER BY
  province_name = 'Ontario' DESC,
  province_name
```
