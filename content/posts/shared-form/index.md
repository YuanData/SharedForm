---
title: "ChatGPT Shared Chat"
date: 2023-08-23T07:00:00+08:00
lastmod: 2023-08-31T07:00:00+08:00
draft: false
markup: HTML
---
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shared Chat</title>
    <!-- Bootstrap CSS -->
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
</head>
<body>
<br>
<blockquote><a href="../readme/">架構與技術說明</a></blockquote>
<div class="container mt-5">
    <table class="table table-bordered">
        <thead>
            <tr>
                <th>ID</th>
                <th>Shared Chat</th>
                <th>Create at</th>
            </tr>
        </thead>
        <tbody id="userTable"></tbody>
    </table>
    
    <nav>
        <ul class="pagination">
            <li class="page-item">
                <button class="page-link" onclick="previousPage()"> &lt; </button>
            </li>
            <li class="page-item">
                <input type="number" id="pageNumber" value="1" class="form-control" style="width: 80px;" onchange="jumpToPage()">
            </li>
            <li class="page-item">
                <button class="page-link" onclick="nextPage()"> &gt; </button>
            </li>
        </ul>
        <div id="pageMessage"></div>
    </nav>
</div>

<!-- Bootstrap JS, Popper.js, and jQuery -->
<script src="https://code.jquery.com/jquery-3.5.1.slim.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@popperjs/core@2.9.3/dist/umd/popper.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/js/bootstrap.min.js"></script>

<!-- Custom JS -->
<script>
    let currentPage = 1;
    const totalPages = 10;  // Dynamic total pages

    let dataSource = "staticJson"; // Global flag variable for data source
    const fetchFunc = {
        "staticJson": fetchStaticJson,
        "localhost": fetchLocalhost,
    };

    // Function to handle fetching data from different sources
    function loadData(page) {
        if(fetchFunc[dataSource]) {
            fetchFunc[dataSource](page);
        } else {
            console.error('Unknown data source');
        }
    }

    function fetchStaticJson(page) {
        const url = `https://raw.githubusercontent.com/YuanData/urlhash/main/sharedlinks/page_id_${page}.json`;
        fetch(url).then(response => response.json())
            .then(data => renderTable(data, page))
            .catch(error => console.error('Error:', error));
    }

    function fetchLocalhost(page) {
        const url = `http://localhost:8080/sharedlinks?page_id=${page}&page_size=10`;
        fetch(url).then(response => response.json())
            .then(data => renderTable(data, page))
            .catch(error => console.error('Error:', error));
    }

    function renderTable(data, page) {
        let tableBody = document.getElementById("userTable");
        tableBody.innerHTML = '';
        data.forEach(resp => {
            tableBody.innerHTML += `
        <tr>
            <td>${resp.id}</td>
            <td><a href="https://chat.openai.com/share/${resp.urlhash}" target="_blank">${resp.name}</a></td>
            <td>${resp.created_at.substring(0, 19)}</td>
        </tr>
            `;
        });

        document.getElementById("pageNumber").value = page;
    }


    function nextPage() {
        if(currentPage < totalPages) {
            currentPage++;
            loadData(currentPage);
            document.getElementById("pageMessage").innerText = "";
        } else {
            document.getElementById("pageMessage").innerText = "已經到最後頁";
        }
    }

    function previousPage() {
        if(currentPage > 1) {
            currentPage--;
            loadData(currentPage);
            document.getElementById("pageMessage").innerText = "";
        } else {
            document.getElementById("pageMessage").innerText = "已經到第1頁";
        }
    }

    function jumpToPage() {
        const desiredPage = Number(document.getElementById("pageNumber").value);
        if(desiredPage >= 1 && desiredPage <= totalPages) {
            currentPage = desiredPage;
            loadData(currentPage);
            document.getElementById("pageMessage").innerText = "";
        } else if (desiredPage < 1) {
            document.getElementById("pageMessage").innerText = "已經到第1頁";
        } else {
            document.getElementById("pageMessage").innerText = "已經到最後頁";
        }
    }

    // Load the first page by default
    loadData(1);
</script>
</body>
</html>
