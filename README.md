# Ticcio Parties 🔥

**A real-time multiplayer party game platform built for live events.**  
Players join on their own phones, compete in challenges, vote each other out Survivor-style.

---

## Live URLs

| Service | URL |
|---------|-----|
| Frontend (Netlify) | https://ticcio-parties.netlify.app |
| Backend (Railway) | https://ticcio-parties.up.railway.app |

---

## Repos

| Repo | Purpose |
|------|---------|
| `ticcio-parties` | Frontend — single `index.html`, auto-deploys to Netlify on push |
| `ticcio-parties-server` | Backend — Express server, auto-deploys to Railway on push |

---

## Deploy Workflow

```bash
# Frontend update
cd ticcio-parties
# drop new index.html in folder
git add .
git commit -m "describe change"
git push
# Netlify auto-deploys in ~30 seconds

# Backend update  
cd ticcio-parties-server
# drop new server.js in folder
git add .
git commit -m "describe change"
git push
# Railway auto-deploys in ~60 seconds
```

---

## Architecture

- **State**: Stored in Railway server memory (resets on server restart)
- **Sync**: Players poll `/state` every 2.5 seconds
- **Auth**: Host password (default: `ticcio`) — set during game setup
- **Images**: Client-side JS database (`PIC_DB`) for picture challenges — no server needed

---

## Game Flow

```
Setup → Players Join → Challenge → Score → Immunity → Vote → Eliminate → Next Round
```

1. **Host logs in** → password: `ticcio` (or whatever was set at setup)
2. **Host sets up game** → enters player names, assigns teams, sets immunity type
3. **Players join** → go to URL, type their name (must match host's roster)
4. **Each round:**
   - Host launches a challenge (Games tab)
   - Players compete in real-time
   - Host ends challenge → scores appear
   - Host grants immunity (form appears automatically)
   - Host opens voting → players vote on their phones
   - Host closes voting → reveals elimination (dramatic modal on all screens)
   - Host hits Next Round → cycle repeats
5. **Game ends** → last survivor wins, confetti fires, stats shown

---

## Games

| Game | How It Works | Scoring |
|------|-------------|---------|
| 🖼️ Picture ID | Players identify 5 images, type answers | Exact match + host override |
| ⚡ Lightning Trivia | 5 multiple choice questions, timed | Speed bonus: <5s=3pt, <10s=2pt, else 1pt |
| 🔢 Sort It | Drag 4 items into correct order | Speed bonus: all correct=10-13pt, 3=6-8pt, etc |
| 🎵 Name That Tune | Host plays YouTube clip, players guess artist/song | Host scores: both=2pt, song only=1pt |
| 🏃 Physical Challenge | Real-world event, host designates winner | Host manually grants immunity |

---

## Voting Rules

- **Individual mode**: All alive players vote. Immune player CAN vote but cannot be voted for.
- **Team mode**: Only the losing team votes. Players can only vote for their own team members.
- **Double elimination**: Host toggles before opening voting. Two dramatic reveals back to back.

---

## Host Controls (Quick Reference)

| Action | Where |
|--------|-------|
| Launch challenge | Games tab |
| End challenge + see scores | Games tab (End button) |
| Grant immunity | Games tab (immunity form, appears after challenge ends) |
| Reassign players to teams | Tribe tab → Tribe Management panel |
| Merge tribes | Tribe tab → Merge field + ⚔️ button |
| Open/close voting | Vote tab |
| Reveal elimination | Vote tab → Reveal button |
| Double elimination | Vote tab → toggle before opening |
| Next round | Vote tab → Next Round button |
| End game manually | Vote tab → End Game button |
| Start new game | Vote tab → End Game → Start New Game (or History tab) |
| Reset game (emergency) | Browser console: `fetch('https://ticcio-parties.up.railway.app/reset', {method:'POST', headers:{'Content-Type':'application/json'}, body:JSON.stringify({hostPass:'ticcio'})})` |

---

## Host Flow Checklist

The Vote tab shows a live 6-step checklist each round:

1. ☐ Launch Challenge
2. ☐ End Challenge + Score  
3. ☐ Grant Immunity
4. ☐ Open Voting
5. ☐ Close & Reveal
6. ☐ Next Round

Active step is highlighted in orange with a hint of what to do next.

---

## Merge Warning

The Tribe tab shows an orange warning banner when:
- Any team drops to ≤1 player
- Teams are imbalanced by 3+ players

Use the Merge field to combine all players into one tribe (e.g. "No Mercy") for individual play.

---

## Adding More Questions

**Sort It** — edit `SORT_DB` in `server.js`. Each category has questions with `items[]` in correct order.  
**Trivia** — edit `TRIVIA_DB` in `server.js`. Each question needs `q`, `options[]`, `correct` (index), `difficulty` (1-5).  
**Pictures** — edit `PIC_DB` in `index.html`. Each question needs `image` URL, `answers[]`, `hint`.

---

## Known Limitations

- Server state resets if Railway restarts (Railway restarts ~once/day on free tier)
- Image URLs are from Wikimedia Commons — load fine in browsers but can occasionally be unavailable
- YouTube autoplay on iOS Safari may require a tap to start audio in Name That Tune
- Max ~30 players recommended (polling at 2.5s with 30 clients is fine)

---

## Session History

| Session | What Was Built |
|---------|---------------|
| 1 | Core app, host setup, tribe grid, voting, elimination modal |
| 2 | Railway backend, real-time multiplayer sync, teams, physical challenges |
| 3 | Picture ID, Lightning Trivia, Sort It, Name That Tune |
| 4 | Winner ceremony, game history log, host flow checklist, UI polish, bug fixes |

---

*Built with Claude · Ticcio LLC · 2026*
