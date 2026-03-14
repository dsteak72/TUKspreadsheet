# TUKspreadsheet
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Unit Management System</title>

<style>
body{
    background:white;
    color:black;
}

h1{
text-align:center;
}

.container{
max-width:900px;
margin:auto;
}

input,select,button{
padding:8px;
margin:5px;
}

button{
cursor:pointer;
}

table{
width:100%;
border-collapse:collapse;
margin-top:20px;
}

th,td{
border:1px solid #555;
padding:10px;
text-align:center;
}

th{
background:#333;
}

.removeBtn{
background:crimson;
color:white;
border:none;
}

.editBtn{
background:orange;
border:none;
}

.saveBtn{
background:limegreen;
border:none;
}

.hidden{
display:none;
}

.authBox{
text-align:center;
margin-top:60px;
}

  h1, h2, h3, h4, h5, h6{
    color:black;
}
</style>
</head>

<body>

<div class="container">

<!-- LOGIN / SIGNUP -->
<div id="authSection" class="authBox">

<h1>Unit Management Login</h1>

<input id="loginUser" placeholder="Username">
<input id="loginPass" type="password" placeholder="Password">

<br>

<button onclick="login()">Login</button>
<button onclick="signup()">Sign Up</button>

<p id="authMessage"></p>

</div>

<!-- MAIN APP -->
<div id="appSection" class="hidden">

<h1>Member Management</h1>

<button onclick="logout()">Logout</button>

<form id="memberForm">

<input type="text" id="username" placeholder="Username" required>

<select id="rank">
<option>Fencible</option>
<option>Private</option>
<option>Guardsman</option>
<option>Lance Corporal</option>
<option>Kingsman</option>
<option>Corporal</option>
<option>Sergeant</option>
<option>Colour Sergeant</option>
<option>Sergeant Major</option>
<option>Officer Cadet</option>
<option>Ensign</option>
<option>Lieutenant</option>
<option>Captain</option>
<option>Major</option>
<option>Lieutenant Colonel</option>
<option>Colonel</option>
</select>

<input type="number" id="strikes" placeholder="Strikes" min="0" required>

<label>LOA <input type="checkbox" id="loa"></label>

<label>NCO Candidate <input type="checkbox" id="nco"></label>

<button type="submit">Add Member</button>

</form>

<table>

<thead>

<tr>
<th>Username</th>
<th>Rank</th>
<th>Strikes</th>
<th>LOA</th>
<th>NCO Candidate</th>
<th>Edit</th>
<th>Remove</th>
</tr>

</thead>

<tbody id="memberTable"></tbody>

</table>

</div>

</div>

<script>

/* ---------- LOGIN SYSTEM ---------- */

function signup(){

let user = document.getElementById("loginUser").value
let pass = document.getElementById("loginPass").value

if(!user || !pass){
document.getElementById("authMessage").innerText="Enter username and password"
return
}

if(localStorage.getItem("user_"+user)){
document.getElementById("authMessage").innerText="Username already exists. Please log in."
return
}

localStorage.setItem("user_"+user, pass)

document.getElementById("authMessage").innerText="Account created! You can now log in."

}

function login(){

let user=document.getElementById("loginUser").value
let pass=document.getElementById("loginPass").value

let savedPass=localStorage.getItem("user_"+user)

if(savedPass===pass){

localStorage.setItem("currentUser",user)

document.getElementById("authSection").classList.add("hidden")
document.getElementById("appSection").classList.remove("hidden")

loadMembers()

}else{

document.getElementById("authMessage").innerText="Invalid login"

}

}

function logout(){

document.getElementById("appSection").classList.add("hidden")
document.getElementById("authSection").classList.remove("hidden")

}


/* ---------- MEMBER SYSTEM ---------- */

const form=document.getElementById("memberForm")
const table=document.getElementById("memberTable")

function saveMembers(){

let rows=document.querySelectorAll("#memberTable tr")
let members=[]

rows.forEach(row=>{

let cells=row.querySelectorAll("td")

members.push({
username:cells[0].innerText,
rank:cells[1].innerText,
strikes:cells[2].innerText,
loa:cells[3].innerText,
nco:cells[4].innerText
})

})

let user=localStorage.getItem("currentUser")

localStorage.setItem("members_"+user,JSON.stringify(members))

}


function loadMembers(){

table.innerHTML=""

let user=localStorage.getItem("currentUser")
let members=JSON.parse(localStorage.getItem("members_"+user))||[]

members.forEach(m=>{

addRow(m.username,m.rank,m.strikes,m.loa,m.nco)

})

}


function addRow(username,rank,strikes,loa,nco){

const row=document.createElement("tr")

row.innerHTML=`
<td>${username}</td>
<td>${rank}</td>
<td>${strikes}</td>
<td>${loa}</td>
<td>${nco}</td>
<td><button class="editBtn">Edit</button></td>
<td><button class="removeBtn">Remove</button></td>
`

table.appendChild(row)

}


form.addEventListener("submit",function(e){

e.preventDefault()

const username=document.getElementById("username").value
const rank=document.getElementById("rank").value
const strikes=document.getElementById("strikes").value
const loa=document.getElementById("loa").checked?"Yes":"No"
const nco=document.getElementById("nco").checked?"Yes":"No"

addRow(username,rank,strikes,loa,nco)

form.reset()

saveMembers()

})


table.addEventListener("click",function(e){

if(e.target.classList.contains("removeBtn")){

e.target.closest("tr").remove()

saveMembers()

}


if(e.target.classList.contains("editBtn")){

let row=e.target.closest("tr")
let cells=row.querySelectorAll("td")

let username=cells[0].innerText
let rank=cells[1].innerText
let strikes=cells[2].innerText
let loa=cells[3].innerText
let nco=cells[4].innerText

cells[0].innerHTML=`<input value="${username}">`
cells[1].innerHTML=`<input value="${rank}">`
cells[2].innerHTML=`<input type="number" value="${strikes}">`
cells[3].innerHTML=`<select>
<option ${loa=="Yes"?"selected":""}>Yes</option>
<option ${loa=="No"?"selected":""}>No</option>
</select>`
cells[4].innerHTML=`<select>
<option ${nco=="Yes"?"selected":""}>Yes</option>
<option ${nco=="No"?"selected":""}>No</option>
</select>`

e.target.textContent="Save"
e.target.classList.remove("editBtn")
e.target.classList.add("saveBtn")

}

else if(e.target.classList.contains("saveBtn")){

let row=e.target.closest("tr")
let cells=row.querySelectorAll("td")

cells[0].textContent=cells[0].querySelector("input").value
cells[1].textContent=cells[1].querySelector("input").value
cells[2].textContent=cells[2].querySelector("input").value
cells[3].textContent=cells[3].querySelector("select").value
cells[4].textContent=cells[4].querySelector("select").value

e.target.textContent="Edit"
e.target.classList.remove("saveBtn")
e.target.classList.add("editBtn")

saveMembers()

}

})

</script>

</body>
</html>
