// ======================
// FIREBASE SETUP
// ======================
const firebaseConfig = {
  apiKey: "AIzaSyAa1_94L8-t6UhRZcpFaMak7s4B9lVMto4",
  authDomain: "timekeeper-30e1b.firebaseapp.com",
  databaseURL: "https://timekeeper-30e1b-default-rtdb.asia-southeast1.firebasedatabase.app",
  projectId: "timekeeper-30e1b",
  storageBucket: "timekeeper-30e1b.firebasestorage.app",
  messagingSenderId: "487732645711",
  appId: "1:487732645711:web:13ef309fb6580e170dff3a"
};

firebase.initializeApp(firebaseConfig);
const db = firebase.database();

const usersRef = db.ref("users");
const dutiesRef = db.ref("duties");
const galleryRef = db.ref("gallery");

// ======================
// VARIABLES
// ======================
let users = [];
let duties = [];
let gallery = {};

const adminPassword = "100AkerWood";

// ======================
// LOAD DATA
// ======================
usersRef.on("value", snapshot => {
    users = snapshot.val() ? Object.values(snapshot.val()) : [];
    renderUserDropdown();
    renderUserManager();
});

dutiesRef.on("value", snapshot => {
    duties = snapshot.val() ? Object.values(snapshot.val()) : [];

    if (document.getElementById("page2").style.display !== "none") {
        showHistory();
    }

    if (document.getElementById("page4").style.display !== "none") {
        showDutyHistory();
    }
});

galleryRef.on("value", snapshot => {
    gallery = snapshot.val() || {};

    if (document.getElementById("page3").style.display !== "none") {
        showGallery();
    }
});

// ======================
// PAGE CONTROL
// ======================
function hideAllPages() {
    document.getElementById("page1").style.display = "none";
    document.getElementById("adminPage").style.display = "none";
    document.getElementById("page2").style.display = "none";
    document.getElementById("page3").style.display = "none";
    document.getElementById("page4").style.display = "none";
    document.getElementById("page5").style.display = "none";
    document.getElementById("page6"
        ).style.display = "none";
}

function showPage(pageId) {
    hideAllPages();
    document.getElementById(pageId).style.display = "block";

    if (pageId === "page2") showHistory();
    if (pageId === "page3") showGallery();
    if (pageId === "page4") showDutyHistory();
    if (pageId === "page5") renderUserManager();
}

window.showPage = showPage;

// ======================
// ADMIN LOGIN
// ======================
function openAdminMenu() {
    let password = prompt("กรอกรหัสแอดมิน");

    if (password !== adminPassword) {
        alert("รหัสไม่ถูกต้อง");
        return;
    }

    showPage("adminPage");
}

// ======================
// THEME
// ======================
function toggleTheme() {
    document.body.classList.toggle("dark-mode");

    const btn = document.getElementById("themeBtn");

    if (document.body.classList.contains("dark-mode")) {
        localStorage.setItem("theme", "dark");
        btn.innerHTML = "☀️ โหมดสว่าง";
    } else {
        localStorage.setItem("theme", "light");
        btn.innerHTML = "🌙 โหมดมืด";
    }
}

if (localStorage.getItem("theme") === "dark") {
    document.body.classList.add("dark-mode");
}

window.onload = function () {
    showPage("page1");

    const btn = document.getElementById("themeBtn");
    if (btn) {
        btn.innerHTML =
            document.body.classList.contains("dark-mode")
                ? "☀️ โหมดสว่าง"
                : "🌙 โหมดมืด";
    }
};

// ======================
// USER DROPDOWN
// ======================
function renderUserDropdown() {
    const select = document.getElementById("name");

    select.innerHTML = `
        <option value="">-- เลือกชื่อ --</option>
    `;

    users.forEach(user => {
        select.innerHTML += `
            <option value="${user.name}">
                ${user.name}
            </option>
        `;
    });
}

// ======================
// SAVE TIM!E
// ======================
function saveTime() {
    const name = document.getElementById("name").value;
    const start = document.getElementById("start").value;
    const end = document.getElementById("end").value;
    const proof = document.getElementById("proof").files[0];

    if (!name || !start || !end) {
        alert("กรอกข้อมูลให้ครบ");
        return;
    }

    if (!proof) {
        alert("กรุณาแนบรูป");
        return;
    }

    let startMinute =
        parseInt(start.split(":")[0]) * 60 +
        parseInt(start.split(":")[1]);

    let endMinute =
        parseInt(end.split(":")[0]) * 60 +
        parseInt(end.split(":")[1]);

    let totalMinute = endMinute - startMinute;

    if (totalMinute <= 0) {
        alert("เวลาไม่ถูกต้อง");
        return;
    }

    let hour = Math.floor(totalMinute / 60);
    let minute = totalMinute % 60;

    let now = new Date();

    let date = now.toLocaleDateString("th-TH");
    let checkIn = now.toLocaleTimeString("th-TH", {
        hour: "2-digit",
        minute: "2-digit"
    });

    const newDuty = {
        name,
        start,
        end,
        hour,
        minute,
        date,
        checkIn
    };

    dutiesRef.push(newDuty);

    let reader = new FileReader();

    reader.onload = function () {
        if (!gallery[name]) {
            gallery[name] = {};
        }

        galleryRef.child(name).push({
            date,
            time:checkIn,
            image: reader.result
        });
    };

    reader.readAsDataURL(proof);

    document.getElementById("result").style.display = "block";
    document.getElementById("result").innerHTML = `
        <h3>✅ บันทึกสำเร็จ</h3>
        ชื่อ: ${name}<br><br>
        เวลา: ${start} - ${end}<br><br>
        รวมเวลา: ${hour} ชั่วโมง ${minute} นาที
    `;

    document.getElementById("start").value = "";
    document.getElementById("end").value = "";
    document.getElementById("proof").value = "";
}

// ======================
// SUMMARY PAGE
// ======================
function showHistory() {
    let summary = {};
    let html = "";

    duties.forEach(item => {
        let totalMinute = (item.hour * 60) + item.minute;

        if (summary[item.name]) {
            summary[item.name] += totalMinute;
        } else {
            summary[item.name] = totalMinute;
        }
    });

    for (let name in summary) {
        let totalMinute = summary[name];
        let hour = Math.floor(totalMinute / 60);
        let minute = totalMinute % 60;

        html += `
            <div class="history-card">
                <b>${name}</b><br>
                รวม ${hour} ชั่วโมง ${minute} นาที
                <br><br>

                <button onclick="deletePersonData('${name}')">
                    🗑️ ลบข้อมูลคนนี้
                </button>
            </div>
        `;
    }

    if (html === "") {
        html = "<p>ยังไม่มีข้อมูล</p>";
    }

    document.getElementById("history").innerHTML = html;
}

function deletePersonData(name) {
    if (!confirm("ลบข้อมูลของ " + name + " ?")) return;

    let newDuties = duties.filter(item => item.name !== name);
    dutiesRef.set(newDuties);
}

function clearAllData() {
    if (!confirm("ลบข้อมูลเวลาทั้งหมด?")) return;

    dutiesRef.set([]);
}

// ======================
// GALLERY PAGE
// ======================
function showGallery() {
    let html = "";
    let names = Object.keys(gallery);

    names.forEach(name => {
        html += `
            <div class="gallery-card">
                <b>👤 ${name}</b>
                <br><br>

                <button onclick="showPersonImages('${name}')">
                    📂 ดูรูป
                </button>

                <button onclick="deletePersonImages('${name}')">
                    🗑️ ลบรูป
                </button>
            </div>
        `;
    });

    if (html === "") {
        html = "<p>ยังไม่มีรูป</p>";
    }

    document.getElementById("gallery").innerHTML = html;
    document.getElementById("personGallery").innerHTML = "";
}

function showPersonImages(name) {
    
    showPage("page6");
    
    let html = `<h3>📸 รูปของ ${name}</h3>`;

    if (!gallery[name]) {
        html += "<p>ไม่มีรูป</p>";
    } else {
        Object.values(gallery[name]).forEach(item => {
            html += `
                <div class="gallery-card">
                    📅 ${item.date}
                    <br><br>
                    🕒${item.time}

                    <img src="${item.image}"
                    style="
                        width:100%;
                        max-width:300px;
                        border-radius:15px;
                    ">
                </div>
            `;
        });
    }

    document.getElementById("personGallery").innerHTML = html;
}

function deletePersonImages(name) {
    if (!confirm("ลบรูปทั้งหมดของ " + name + " ?")) return;

    galleryRef.child(name).remove();
}

function clearGallery() {
    if (!confirm("ลบรูปทั้งหมด?")) return;

    galleryRef.remove();
}

// ======================
// HISTORY PAGE
// ======================
function showDutyHistory() {
    let keyword =
        document.getElementById("historySearch")
        .value
        .toLowerCase();

    let html = "";

    duties
        .slice()
        .reverse()
        .forEach(item => {
            let searchText =
                (
                    item.name +
                    item.date +
                    item.checkIn
                ).toLowerCase();

            if (!searchText.includes(keyword)) {
                return;
            }

            html += `
    <div class="history-card">

        📅 ${item.date} | 🕒 ${item.checkIn}
        <br><br>

        👤 ชื่อ: ${item.name}
        <br>

        ⏰ เวลาเข้าเวร:
        ${item.start} - ${item.end}
        <br>

        ⌛ รวมเป็น:
        ${item.hour} ชั่วโมง ${item.minute} นาที

    </div>
`;
        });

    if (html === "") {
        html = "<p>ไม่พบข้อมูล</p>";
    }

    document.getElementById("historyLog").innerHTML = html;
}

function clearHistory() {
    if (!confirm("ลบประวัติทั้งหมด?")) return;

    dutiesRef.set([]);
}

// ======================
// USER MANAGER
// ======================
function addUser() {
    const input = document.getElementById("newUserName");
    const name = input.value.trim();

    if (!name) {
        alert("กรุณากรอกชื่อ");
        return;
    }

    let alreadyExists = users.some(
        user => user.name.toLowerCase() === name.toLowerCase()
    );

    if (alreadyExists) {
        alert("ชื่อนี้มีอยู่แล้ว");
        return;
    }

    usersRef.push({
        name: name
    });

    input.value = "";
}

function deleteUser(name) {
    if (!confirm("ลบชื่อ " + name + " ?")) return;

    usersRef.once("value", snapshot => {
        const data = snapshot.val();
        if (!data) return;

        Object.keys(data).forEach(key => {
            if (data[key].name === name) {
                usersRef.child(key).remove();
            }
        });
    });
}

function renderUserManager() {
    const box = document.getElementById("userList");

    if (!box) return;

    let html = "";

    users.forEach(user => {
        html += `
            <div class="history-card">
                <b>${user.name}</b>
                <br><br>

                <button onclick="deleteUser('${user.name}')">
                    🗑️ ลบชื่อ
                </button>
            </div>
        `;
    });

    if (html === "") {
        html = "<p>ยังไม่มีรายชื่อ</p>";
    }

    box.innerHTML = html;
}

// ======================
// EXPORT TO WINDOW
// ======================
window.saveTime = saveTime;
window.openAdminMenu = openAdminMenu;
window.toggleTheme = toggleTheme;

window.clearAllData = clearAllData;
window.clearGallery = clearGallery;
window.clearHistory = clearHistory;

window.showPersonImages = showPersonImages;
window.deletePersonImages = deletePersonImages;
window.deletePersonData = deletePersonData;

window.addUser = addUser;
window.deleteUser = deleteUser;
window.showDutyHistory = showDutyHistory;