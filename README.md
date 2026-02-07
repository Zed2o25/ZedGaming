# 🎮 ZedGaming - Group Game

## ✅ Built Successfully!

### How to Test Locally:

1. **Open the game:**
   - Just double-click `game.html` in your browser
   - Or right-click → Open with → Chrome/Edge/Firefox

2. **Test on your phone:**
   - Use your computer's IP address
   - Share via USB cable or local network

---

## 🌐 Free Hosting Options (No Credit Card):

### Option 1: GitHub Pages (Recommended - Easiest)
```bash
# 1. Create a GitHub account (free)
# 2. Install Git: https://git-scm.com/downloads
# 3. Run these commands in PowerShell:

cd "c:\Users\ITFZ\Desktop\ZedGaming"
git init
git add game.html manifest.json
git commit -m "Initial game"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/zedgaming.git
git push -u origin main

# 4. On GitHub.com:
#    - Go to Settings → Pages
#    - Source: main branch
#    - Your game will be at: https://YOUR_USERNAME.github.io/zedgaming/game.html
```

### Option 2: Netlify (Drag & Drop - Super Easy!)
1. Go to: https://app.netlify.com/drop
2. Drag the `ZedGaming` folder onto the page
3. Done! You get a URL like: https://random-name.netlify.app

### Option 3: Vercel (For Developers)
```bash
# Install Vercel CLI
npm install -g vercel

# Deploy
cd "c:\Users\ITFZ\Desktop\ZedGaming"
vercel

# Follow prompts - gets a URL like: https://your-game.vercel.app
```

### Option 4: Cloudflare Pages
1. Sign up: https://pages.cloudflare.com
2. Connect GitHub or drag & drop
3. Free unlimited bandwidth!

---

## 📱 How It Works:

### For Mobile Users:
1. Open the game URL on your phone
2. **Create Room** → Get a 4-digit code
3. Share the code with friends
4. They enter the code to join
5. Select a game and play!

### Games Included:
- **🎨 Color Game**: Fast color matching
- **🧠 Quiz Game**: Arabic trivia questions  
- **🔢 Number Game**: Guess closest to random number

### Features:
- ✅ Mobile-optimized
- ✅ Arabic (RTL) interface
- ✅ Works offline after first load
- ✅ Room code system
- ✅ Live scoreboard
- ✅ Can be installed as app (PWA)

---

## 🚀 Quick Deploy with Netlify (60 seconds):

1. Go to: https://app.netlify.com/drop
2. Drag your `ZedGaming` folder
3. Copy the URL
4. Share with friends!

---

## 🔄 To Add Real Multiplayer (Future):

Current version uses room codes but scores are local. To make it truly multiplayer:

### Free Backend Options:
- **Firebase** (Google - Free tier: https://firebase.google.com)
- **Supabase** (Free PostgreSQL: https://supabase.com)
- **PocketBase** (Self-hosted: https://pocketbase.io)

I can help you add real-time sync once you pick a hosting platform!

---

## 📝 Notes:

- Current version: Works with room codes (manual coordination)
- Best for: 2-10 players in same chat room
- Perfect for: Testing and small groups
- No backend needed for basic testing!

---

**Ready to host it? Let me know which platform you prefer!**
