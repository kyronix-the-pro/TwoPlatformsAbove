    const t = document.getElementById("postTitle").value.trim();
    if(!base64Media || !t) return alert("Fill in everything!");

    await fetch('/api/posts', {
        method: 'POST',
        headers: {'Content-Type': 'application/json'},
        body: JSON.stringify({ author: currentUser.username, title: t, data: base64Media, type: mediaType })
    });
    showFeedPage();
}
</script>
</body>
</html>
    `);
});

// 2. BACKEND API ROUTES (Handles live global communication syncing)
app.get('/api/posts', (req, res) => res.json(sharedPosts));
app.post('/api/posts', (req, res) => { sharedPosts.push(req.body); res.json({ success: true }); });
app.get('/api/users', (req, res) => res.json(sharedUsers));
app.post('/api/users', (req, res) => { sharedUsers.push(req.body); res.json({ success: true }); });

// 3. START SERVER
app.listen(PORT, '0.0.0.0', () => {
    console.log("==================================================");
    console.log("🚀 SNAPIO LIVE INTER-DEVICE HUB IS ONLINE!");
    console.log("To view it on your computer: http://localhost:3000");
    console.log("\\nTo let other phones/devices connect to your feed:");
    console.log("1. Connect them to your same local network / Wi-Fi.");
    console.log("2. Find your machine's local IP address.");
    console.log("3. Type: http://<YOUR_IP_ADDRESS>:3000 into their browser!");
    console.log("==================================================");
});
