# Student Information Management System

## Overview

This project is a simple web application designed to manage student information. It allows users to input and store student details using HTML, JavaScript, and the JSON Power Database.

## Features

- **Student Information Form:** Easily input and submit student details.
- **Data Storage:** Utilizes JSON Power Database for efficient data storage.
- **Validation:** Includes form validation to ensure accurate data entry.
- **Responsive Design:** User-friendly interface that adapts to different screen sizes.

## Technologies Used

- HTML
- JavaScript
- [JSON Power Database]

# Code
<!DOCTYPE html>
<html lang="en">
<head>
    <title>Bootstrap Example</title>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css">
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.4.1/js/bootstrap.min.js"></script>
    <script src="http://login2explore.com/jpdb/resources/js/0.0.3/jpdb-commons.js"></script>
</head>
<body>
    <div class="container">
        <div class="page-header text-center">
            <h2>Student Enrollment Form</h2>
        </div>
        <div class="page-header text-center">
            <form id="StuForm" method="get">
                <div class="form-group text-center">
                    <label>Roll No:</label>
                    <input type="number" id="rollno" class="form-control" onchange="getroll()">
                </div>
                <div class="form-group text-center">
                    <label>Full Name:</label>
                    <input type="text" id="name" class="form-control">
                </div>
                <div class="form-group text-center">
                    <label>Class:</label>
                    <input type="text" id="class" class="form-control">
                </div>
                <div class="form-group text-center">
                    <label>Birth date:</label>
                    <input type="date" id="bdate" class="form-control">
                </div>
                <div class="form-group text-center">
                    <label>Address:</label>
                    <input type="text" id="address" class="form-control">
                </div>
                <div class="form-group text-center">
                    <label>Enrollment Date:</label>
                    <input type="date" id="edate" class="form-control">
                </div>
                <div class="form-group text-center">
                    <button type="button" class="btn btn-lg btn-primary" id="save" onclick="saveData()" disabled>Save</button>
                    <button type="button" class="btn btn-lg btn-primary" id="reset" onclick="resetForm()" disabled>Reset</button>
                </div>
            </form>
        </div>
    </div>

    <script>
        function validateData() {
            var rollnov = $("#rollno").val();
            if (rollnov === "") {
                alert("Roll No Required Value");
                $("#rollno").focus();
                return null;
            }
            var namev = $("#name").val();
            if (namev === "") {
                alert("Full Name Required Value");
                $("#name").focus();
                return null;
            }
            var classv = $("#class").val();
            if (classv === "") {
                alert("Class Required Value");
                $("#class").focus();
                return null;
            }
            var bdatev = $("#bdate").val();
            if (bdatev === "") {
                alert("Birth date Required Value");
                $("#bdate").focus();
                return null;
            }
            var addv = $("#address").val();
            if (addv === "") {
                alert("Address is Required Value");
                $("#address").focus();
                return null;
            }
            var edatev = $("#edate").val();
            if (edatev === "") {
                alert("Enrollment is Required Value");
                $("#edate").focus();
                return null;
            }
            var jsonStrObj = {
                rollno: rollnov,
                name: namev,
                class: classv,
                bdate: bdatev,
                address: addv,
                edate: edatev,
            };
            return JSON.stringify(jsonStrObj);
        }

        function resetForm() {
            $("#rollno").val("");
            $("#name").val("");
            $("#class").val("");
            $("#bdate").val("");
            $("#address").val("");
            $("#edate").val("");
            $("#rollno").prop("disabled", false);
            $("#save").prop("disabled", true);
            $("#update").prop("disabled", true);
            $("#reset").prop("disabled", true);
            $("#rollno").focus();
        }

        function getRollAsJsonObj() {
            var roll_id = $("#rollno").val();
            var jsonStr = {
                id: roll_id
            };
            return JSON.stringify(jsonStr);
        }

        function saveRec(jsonObj) {
            var Data = JSON.parse(jsonObj.data);
            localStorage.setItem("recno", Data.rec_no);
        }

        function fillData(jsonObj) {
            saveRec(jsonObj);
            var data = JSON.parse(jsonObj.data).record;
            $("#rollno").val(data.rollno);
            $("#name").val(data.name);
            $("#class").val(data.class);
            $("#bdate").val(data.bdate)
            $("#address").val(data.address);
            $("#edate").val(data.edate);
        }

        function getroll() {
            var rolljsonobj = getRollAsJsonObj();
            var getRequest = createGETRequest("90931841|-31949301126434821|90962998", "STUDENT_DB", "Student-Table", rolljsonobj);
            jQuery.ajaxSetup({ async: false });
            var resJsonObj = executeCommandAtGivenBaseUrl(getRequest, "http://api.login2explore.com:5577", "/api/irl");
            jQuery.ajaxSetup({ async: true });
            if (resJsonObj.status === 400) {
                alert("Student not found. Save and Reset buttons enabled.");
                $("#save").prop("disabled", false);
                $("#reset").prop("disabled", false);
                $("#name").focus();
            } else if (resJsonObj.status === 200) {
                alert("Student found. Update and Reset buttons enabled.");
                $("#rollno").prop("disabled", true);
                fillData(resJsonObj);
                $("#update").prop("disabled", false);
                $("#reset").prop("disabled", false);
                $("#name").focus();
            }
        }

        function saveData() {
            var jsonStr = validateData();
            if (jsonStr === null) {
                return;
            }
            var putReqStr = createPUTRequest("90931841|-31949301126434821|90962998", jsonStr, "STUDENT_DB", "Student-Table");
            alert(putReqStr);
            jQuery.ajaxSetup({ async: false });
            var resultObj = executeCommandAtGivenBaseUrl(putReqStr, "http://api.login2explore.com:5577", "/api/iml");
            jQuery.ajaxSetup({ async: true });
            alert(JSON.stringify(resultObj));
            resetForm();
            $("#rollno").focus();
        }

        function updateData() {
            $("#update").prop("disabled", true);
            var jsonchg = validateData();
            if (jsonchg === null) {
                return;
            }
            var updateRequest = createUPDATERecordRequest("90931841|-31949301126434821|90962998", jsonchg, "STUDENT_DB", "Student-Table", localStorage.getItem("recno"))
            jQuery.ajaxSetup({ async: false });
            var resultObj = executeCommandAtGivenBaseUrl(updateRequest, "http://api.login2explore.com:5577", "/api/iml");
            jQuery.ajaxSetup({ async: true });
            console.log(resultObj)
            resetForm();
            $("#rollno").focus();
        }
    </script>

</body>
</html>




       
