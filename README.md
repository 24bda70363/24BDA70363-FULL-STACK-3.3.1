# 24BDA70363-FULL-STACK-3.3.1

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Social Media Management Dashboard</title>

<style>
* {
    box-sizing: border-box;
}

body {
    font-family: Arial, sans-serif;
    margin: 0;
    background: #eef2f7;
    color: #1f2937;
}

header {
    background: #1d4ed8;
    color: white;
    padding: 20px;
    text-align: center;
}

.container {
    max-width: 1000px;
    margin: 25px auto;
    padding: 15px;
}

.card {
    background: white;
    padding: 22px;
    margin-bottom: 20px;
    border-radius: 10px;
    box-shadow: 0 2px 8px #00000012;
}

h2 {
    color: #1d4ed8;
}

input, textarea, select {
    width: 100%;
    padding: 11px;
    margin: 8px 0;
    border: 1px solid #cbd5e1;
    border-radius: 5px;
}

textarea {
    min-height: 100px;
}

button {
    background: #2563eb;
    color: white;
    padding: 11px 16px;
    margin: 5px 5px 5px 0;
    border: none;
    border-radius: 5px;
    cursor: pointer;
}

button:hover {
    background: #1e40af;
}

button.danger {
    background: #dc2626;
}

button.secondary {
    background: #64748b;
}

nav {
    display: flex;
    flex-wrap: wrap;
    gap: 5px;
    margin-bottom: 20px;
}

.hidden {
    display: none;
}

.metrics {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
}

.metric {
    flex: 1;
    min-width: 130px;
    padding: 20px;
    background: #dbeafe;
    text-align: center;
    border-radius: 8px;
}

.metric h2 {
    margin: 5px;
}

.post {
    border: 1px solid #e5e7eb;
    padding: 15px;
    margin: 12px 0;
    border-radius: 8px;
}

.post p {
    white-space: pre-wrap;
    overflow-wrap: anywhere;
}

.status {
    color: #059669;
    font-weight: bold;
}

.message {
    color: #166534;
    font-weight: bold;
}

.error {
    color: #dc2626;
}
</style>
</head>

<body>

<header>
<h1>Social Media Management System</h1>
<p>Full Stack Integration Dashboard</p>
</header>

<div class="container">

<!-- AUTHENTICATION -->

<div class="card" id="authSection">

<h2>User Authentication</h2>

<input type="text" id="username"
placeholder="Enter Username">

<input type="password" id="password"
placeholder="Enter Password">

<button onclick="registerUser()">Register</button>
<button onclick="loginUser()">Login</button>

<p id="authMessage"></p>

</div>

<!-- MAIN DASHBOARD -->

<div id="dashboard" class="hidden">

<div class="card">
<h2>Welcome, <span id="welcomeUser"></span></h2>

<button class="danger" onclick="logoutUser()">
Logout
</button>
</div>

<nav>
<button onclick="showSection('composer')">
Create Post
</button>

<button onclick="showSection('feed')">
Post Feed
</button>

<button onclick="showSection('scheduler')">
Scheduler
</button>

<button onclick="showSection('analytics')">
Analytics
</button>
</nav>

<!-- CREATE POST -->

<div class="card section" id="composer">

<h2>Create Social Media Post</h2>

<input type="text" id="postTitle"
placeholder="Enter Post Title">

<textarea id="postContent"
placeholder="Write your post"></textarea>

<select id="platform">
<option value="Facebook">Facebook</option>
<option value="Instagram">Instagram</option>
<option value="Twitter">Twitter</option>
<option value="LinkedIn">LinkedIn</option>
</select>

<button onclick="createPost()">
Publish Post
</button>

<button onclick="saveDraft()" class="secondary">
Save Draft
</button>

<p id="postMessage"></p>

</div>

<!-- POST FEED -->

<div class="card section hidden" id="feed">

<h2>Post Feed</h2>

<input type="text" id="search"
placeholder="Search posts"
oninput="displayPosts()">

<div id="postList"></div>

</div>

<!-- SCHEDULER -->

<div class="card section hidden" id="scheduler">

<h2>Schedule Social Media Post</h2>

<input type="text" id="scheduleTitle"
placeholder="Post Title">

<textarea id="scheduleContent"
placeholder="Post Content"></textarea>

<select id="schedulePlatform">
<option>Facebook</option>
<option>Instagram</option>
<option>Twitter</option>
<option>LinkedIn</option>
</select>

<input type="datetime-local" id="scheduleDate">

<button onclick="schedulePost()">
Schedule Post
</button>

<h3>Scheduled Posts</h3>

<div id="scheduledList"></div>

</div>

<!-- ANALYTICS -->

<div class="card section hidden" id="analytics">

<h2>Analytics Dashboard</h2>

<div class="metrics">

<div class="metric">
<h2 id="totalPosts">0</h2>
Total Posts
</div>

<div class="metric">
<h2 id="publishedPosts">0</h2>
Published
</div>

<div class="metric">
<h2 id="draftPosts">0</h2>
Drafts
</div>

<div class="metric">
<h2 id="scheduledPosts">0</h2>
Scheduled
</div>

</div>

<h3>Platform Statistics</h3>
<div id="platformStats"></div>

</div>

</div>
</div>

<script>

const USERS_KEY = "fsd_demo_users";
const POSTS_KEY = "fsd_demo_posts";
const SESSION_KEY = "fsd_demo_session";

let users = JSON.parse(
    localStorage.getItem(USERS_KEY) || "[]"
);

let posts = JSON.parse(
    localStorage.getItem(POSTS_KEY) || "[]"
);

let currentUser =
    sessionStorage.getItem(SESSION_KEY);

function saveData() {
    localStorage.setItem(
        USERS_KEY, JSON.stringify(users)
    );

    localStorage.setItem(
        POSTS_KEY, JSON.stringify(posts)
    );
}

function registerUser() {
    const username =
        document.getElementById("username").value.trim();

    const password =
        document.getElementById("password").value;

    const message =
        document.getElementById("authMessage");

    if (!username || !password) {
        message.innerText = "Enter username and password.";
        return;
    }

    if (users.some(u => u.username === username)) {
        message.innerText = "User already exists.";
        return;
    }

    users.push({ username, password });
    saveData();

    message.innerText =
        "Registration successful. Please login.";
}

function loginUser() {
    const username =
        document.getElementById("username").value.trim();

    const password =
        document.getElementById("password").value;

    const user = users.find(u =>
        u.username === username &&
        u.password === password
    );

    if (!user) {
        document.getElementById("authMessage").innerText =
            "Invalid username or password.";
        return;
    }

    currentUser = username;
    sessionStorage.setItem(SESSION_KEY, currentUser);
    openDashboard();
}

function openDashboard() {
    document.getElementById("authSection")
        .classList.add("hidden");

    document.getElementById("dashboard")
        .classList.remove("hidden");

    document.getElementById("welcomeUser").textContent =
        currentUser;

    showSection("composer");
}

function logoutUser() {
    currentUser = null;
    sessionStorage.removeItem(SESSION_KEY);

    document.getElementById("dashboard")
        .classList.add("hidden");

    document.getElementById("authSection")
        .classList.remove("hidden");
}

function showSection(id) {
    document.querySelectorAll(".section")
        .forEach(section =>
            section.classList.add("hidden")
        );

    document.getElementById(id)
        .classList.remove("hidden");

    displayPosts();
    displayScheduled();
    updateAnalytics();
}

function addPost(status) {
    const title =
        document.getElementById("postTitle").value.trim();

    const content =
        document.getElementById("postContent").value.trim();

    const platform =
        document.getElementById("platform").value;

    if (!title || !content) {
        alert("Please enter title and content.");
        return;
    }

    posts.push({
        id: Date.now() + Math.random(),
        title,
        content,
        platform,
        status,
        author: currentUser,
        date: new Date().toLocaleString()
    });

    saveData();

    document.getElementById("postTitle").value = "";
    document.getElementById("postContent").value = "";

    document.getElementById("postMessage").innerText =
        status === "PUBLISHED"
        ? "Post published successfully!"
        : "Draft saved successfully!";

    updateAnalytics();
}

function createPost() {
    addPost("PUBLISHED");
}

function saveDraft() {
    addPost("DRAFT");
}

function displayPosts() {
    const list = document.getElementById("postList");

    const search =
        document.getElementById("search")
        .value.toLowerCase();

    list.innerHTML = "";

    const filtered = posts.filter(post =>
        post.author === currentUser &&
        (
            post.title.toLowerCase().includes(search) ||
            post.content.toLowerCase().includes(search)
        )
    );

    if (filtered.length === 0) {
        list.textContent = "No posts found.";
        return;
    }

    filtered.forEach(post => {
        const div = document.createElement("div");
        div.className = "post";

        const title = document.createElement("h3");
        title.textContent = post.title;

        const content = document.createElement("p");
        content.textContent = post.content;

        const info = document.createElement("p");
        info.textContent =
            post.platform + " | " + post.status +
            " | " + post.date;

        div.append(title, content, info);

        if (post.status === "DRAFT") {
            const publish = document.createElement("button");
            publish.textContent = "Publish";
            publish.onclick = () =>
                changeStatus(post.id, "PUBLISHED");
            div.appendChild(publish);
        }

        if (post.status === "PUBLISHED") {
            const archive = document.createElement("button");
            archive.textContent = "Archive";
            archive.onclick = () =>
                changeStatus(post.id, "ARCHIVED");
            div.appendChild(archive);
        }

        const remove = document.createElement("button");
        remove.textContent = "Delete";
        remove.className = "danger";
        remove.onclick = () => deletePost(post.id);

        div.appendChild(remove);
        list.appendChild(div);
    });
}

function changeStatus(id, status) {
    const post = posts.find(p =>
        p.id === id && p.author === currentUser
    );

    if (post) {
        post.status = status;
        saveData();
        displayPosts();
        updateAnalytics();
    }
}

function deletePost(id) {
    if (!confirm("Delete this post?")) return;

    posts = posts.filter(p =>
        !(p.id === id && p.author === currentUser)
    );

    saveData();
    displayPosts();
    displayScheduled();
    updateAnalytics();
}

function schedulePost() {
    const title =
        document.getElementById("scheduleTitle").value.trim();

    const content =
        document.getElementById("scheduleContent").value.trim();

    const platform =
        document.getElementById("schedulePlatform").value;

    const date =
        document.getElementById("scheduleDate").value;

    if (!title || !content || !date) {
        alert("Fill all scheduling details.");
        return;
    }

    if (new Date(date).getTime() <= Date.now()) {
        alert("Please select a future date and time.");
        return;
    }

    posts.push({
        id: Date.now() + Math.random(),
        title,
        content,
        platform,
        status: "SCHEDULED",
        author: currentUser,
        date
    });

    saveData();

    document.getElementById("scheduleTitle").value = "";
    document.getElementById("scheduleContent").value = "";
    document.getElementById("scheduleDate").value = "";

    alert("Post scheduled successfully!");
    displayScheduled();
    updateAnalytics();
}

function displayScheduled() {
    const list =
        document.getElementById("scheduledList");

    list.innerHTML = "";

    const scheduled = posts.filter(p =>
        p.author === currentUser &&
        p.status === "SCHEDULED"
    );

    if (scheduled.length === 0) {
        list.textContent = "No scheduled posts.";
        return;
    }

    scheduled.forEach(post => {
        const div = document.createElement("div");
        div.className = "post";

        const heading = document.createElement("h3");
        heading.textContent = post.title;

        const details = document.createElement("p");
        details.textContent =
            post.platform + " | " +
            new Date(post.date).toLocaleString();

        const remove = document.createElement("button");
        remove.textContent = "Delete";
        remove.className = "danger";
        remove.onclick = () => deletePost(post.id);

        div.append(heading, details, remove);
        list.appendChild(div);
    });
}

function updateAnalytics() {
    const mine = posts.filter(p =>
        p.author === currentUser
    );

    document.getElementById("totalPosts").innerText =
        mine.length;

    document.getElementById("publishedPosts").innerText =
        mine.filter(p => p.status === "PUBLISHED").length;

    document.getElementById("draftPosts").innerText =
        mine.filter(p => p.status === "DRAFT").length;

    document.getElementById("scheduledPosts").innerText =
        mine.filter(p => p.status === "SCHEDULED").length;

    const stats =
        document.getElementById("platformStats");

    stats.innerHTML = "";

    ["Facebook", "Instagram", "Twitter", "LinkedIn"]
        .forEach(platform => {

            const count = mine.filter(p =>
                p.platform === platform
            ).length;

            const p = document.createElement("p");
            p.textContent = platform + ": " + count;
            stats.appendChild(p);
        });
}

if (currentUser && users.some(u =>
    u.username === currentUser
)) {
    openDashboard();
} else {
    currentUser = null;
    sessionStorage.removeItem(SESSION_KEY);
}

</script>

</body>
</html>
