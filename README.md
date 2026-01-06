<!DOCTYPE html>
<html lang="he" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>אופקים LIVE | פורטל החדשות המקומי</title>
    <link href="https://fonts.googleapis.com/css2?family=Assistant:wght@300;400;700&display=swap" rel="stylesheet">
    <style>
        :root {
            --primary: #1a73e8;
            --accent: #f9ab00;
            --dark: #202124;
            --light: #f8f9fa;
        }

        body {
            font-family: 'Assistant', sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 0;
            color: var(--dark);
        }

        header {
            background-color: white;
            border-bottom: 4px solid var(--primary);
            padding: 20px 0;
            text-align: center;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            position: sticky;
            top: 0;
            z-index: 1000;
        }

        .logo h1 { margin: 0; color: var(--primary); font-size: 2.5rem; }
        .logo span { color: var(--accent); }

        .container {
            max-width: 1100px;
            margin: 30px auto;
            padding: 0 20px;
        }

        .news-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 25px;
        }

        .news-card {
            background: white;
            border-radius: 12px;
            overflow: hidden;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
            transition: transform 0.2s;
            display: flex;
            flex-direction: column;
        }

        .news-card:hover { transform: translateY(-5px); }

        .news-content { padding: 20px; flex-grow: 1; }
        
        .news-card h3 {
            margin-top: 0;
            font-size: 1.3rem;
            line-height: 1.4;
            color: var(--dark);
        }

        .meta {
            font-size: 0.85rem;
            color: #70757a;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
        }

        .btn-read {
            display: inline-block;
            background: var(--primary);
            color: white;
            padding: 10px 20px;
            text-decoration: none;
            border-radius: 6px;
            font-weight: bold;
            text-align: center;
        }

        .loader {
            text-align: center;
            padding: 50px;
            font-size: 1.2rem;
        }
    </style>
</head>
<body>

<header>
    <div class="logo">
        <h1>אופקים <span>LIVE</span></h1>
    </div>
    <p>כל מה שקורה בעיר - עדכון אוטומטי</p>
</header>

<div class="container">
    <div id="loader" class="loader">סורק את הרשת עבור עדכונים מאופקים...</div>
    <div class="news-grid" id="news-grid"></div>
</div>

<script>
    // שימוש בשירות חינמי להמרת RSS ל-JSON כדי שנוכל לקרוא את גוגל חדשות
    const RSS_URL = 'https://news.google.com/rss/search?q=%D7%90%D7%95%D7%A4%D7%A7%D7%99%D7%9D&hl=he&gl=IL&ceid=IL:he';
    const API_CONVERTER = `https://api.rss2json.com/v1/api.json?rss_url=${encodeURIComponent(RSS_URL)}`;

    async function fetchNews() {
        try {
            const response = await fetch(API_CONVERTER);
            const data = await response.json();
            
            const container = document.getElementById('news-grid');
            const loader = document.getElementById('loader');
            
            if (data.status === 'ok') {
                loader.style.display = 'none';
                data.items.forEach(item => {
                    // ניקוי שם המקור מהכותרת (גוגל מוסיפה אותו בסוף)
                    const titleParts = item.title.split(' - ');
                    const sourceName = titleParts.pop();
                    const cleanTitle = titleParts.join(' - ');

                    const card = `
                        <div class="news-card">
                            <div class="news-content">
                                <div class="meta">
                                    <span>${sourceName}</span>
                                    <span>${new Date(item.pubDate).toLocaleDateString('he-IL')}</span>
                                </div>
                                <h3>${cleanTitle}</h3>
                                <p>${item.content.substring(0, 100).replace(/<[^>]*>?/gm, '')}...</p>
                            </div>
                            <div style="padding: 0 20px 20px;">
                                <a href="${item.link}" target="_blank" class="btn-read">לכתבה המלאה</a>
                            </div>
                        </div>
                    `;
                    container.innerHTML += card;
                });
            }
        } catch (error) {
            document.getElementById('loader').innerText = "שגיאה בטעינת הנתונים. נסה שוב מאוחר יותר.";
            console.error(error);
        }
    }

    fetchNews();
</script>

</body>
</html>
פורטל החדשות של אופקים
