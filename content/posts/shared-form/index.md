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
<h3><a href="../readme/">README.md [架構與技術說明]</a></h3>
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
    const totalPages = 10;  // This value can be dynamically set if the API provides the total pages information

    function loadUsers(page) {
        fetchData(`http://localhost:8080/sharedlinks?page_id=${page}&page_size=10`, page)
        .catch(error => {
            console.error('Error:', error);
            if (error.message === 'Failed to fetch') {
                return fetchData(`https://raw.githubusercontent.com/YuanData/urlhash/main/sharedlinks/page_id_${page}.json`, page);
            } else {
                throw error;
            }
        })
        .catch(error => console.error('Error:', error));
    }

    function fetchData(url, page) {
        return fetch(url)
        .then(response => response.json())
        .then(data => {
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
        });
    }


    function nextPage() {
        if(currentPage < totalPages) {
            currentPage++;
            loadUsers(currentPage);
            document.getElementById("pageMessage").innerText = "";
        } else {
            document.getElementById("pageMessage").innerText = "已經到最後頁";
        }
    }

    function previousPage() {
        if(currentPage > 1) {
            currentPage--;
            loadUsers(currentPage);
            document.getElementById("pageMessage").innerText = "";
        } else {
            document.getElementById("pageMessage").innerText = "已經到第1頁";
        }
    }

    function jumpToPage() {
        const desiredPage = Number(document.getElementById("pageNumber").value);
        if(desiredPage >= 1 && desiredPage <= totalPages) {
            currentPage = desiredPage;
            loadUsers(currentPage);
            document.getElementById("pageMessage").innerText = "";
        } else if (desiredPage < 1) {
            document.getElementById("pageMessage").innerText = "已經到第1頁";
        } else {
            document.getElementById("pageMessage").innerText = "已經到最後頁";
        }
    }

    // Load the first page by default
    loadUsers(1);
</script>
</body>
</html>
