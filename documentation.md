# Nextbot: Endless - Documentation

A 3D horror / exploration web game in a single HTML file, built with Three.js r128.
Single-player only. No build step and no server code are needed.

## 1. Package contents
- index.html - the complete game (HTML, CSS and JavaScript in one file)
- DOCUMENTATION.md - this file
- LICENSE.txt - license notes

## 2. Requirements
- A modern browser with WebGL (Chrome, Edge, Firefox, Safari; Android and iOS work).
- Internet access on first load, because Three.js is loaded from a CDN (see section 4).
- Sound starts after the first tap or click (browser autoplay rule).

## 3. Local testing
Option A: open index.html in your browser (needs internet for the CDN).
Option B (recommended): run a local server inside the folder:
    python3 -m http.server 8000
then open http://localhost:8000
To test on a phone on the same Wi-Fi: http://YOUR-COMPUTER-IP:8000
Checklist: the menu appears -> tap "Story: The Backrooms" -> tap "Masuk" -> the game starts,
touch controls respond, and sound plays after the first tap.

## 4. Three.js CDN dependency
index.html loads this script:
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
- The game is built and tested with r128. Newer versions change APIs and may break it.
- Offline use: download that file, save it next to index.html as three.min.js and change
  the src attribute to "three.min.js".
- Three.js is MIT licensed (https://threejs.org).

## 5. Deployment
Upload index.html (plus three.min.js if you host it locally) to any static host:
- GitHub Pages: repository Settings -> Pages -> Deploy from a branch -> main, /(root)
- Netlify or Cloudflare Pages: drag and drop the folder
- itch.io (HTML5): upload a ZIP with index.html at the root
- Your own web server: copy the files. HTTPS is recommended.

## 6. Controls
Desktop: WASD move | mouse look (click the game once) | Shift toggle sprint |
C slide / crouch | Space jump (hold to bunny-hop) | left mouse shoot (hold) | R reload.
Mobile: left thumb = move | drag on the right side = look | buttons JUMP, SPRINT (toggle),
SLIDE, FIRE (hold), R. You can drag from the JUMP or FIRE button to keep looking while holding.
Gamepad: left stick move | right stick look | A jump | RT/RB fire | L3 toggle sprint |
B slide | X reload.
Menu sliders: Fog, Sensitivity (saved in the browser), Light.

## 7. Replacing assets and audio
### 7.1 Without editing code
Main menu -> "Custom bot: foto & audio dari HP":
- "Pilih foto": choose an image for Bot 1, 2 or 3 (cropped to a circle). Used while chasing
  and in the jumpscare.
- "Pilih audio kejar": looping chase sound (mp3, wav or ogg). Gets louder and higher when
  the bot is close.
- "Pilih audio jumpscare": one-shot sound at the jumpscare.
Choices are not saved after a reload. Only use images and audio you have the right to use.

### 7.2 Bundling your own files in the code
Open index.html and search for these names.
Bot faces: FACES = [tex, mkFace(1), mkFace(2)] holds one THREE.Texture per bot variant.
Replace an entry with your own image:
    const img=new Image();
    img.onload=()=>{const c=document.createElement('canvas');c.width=c.height=256;
      c.getContext('2d').drawImage(img,0,0,256,256);FACES[0]=new THREE.CanvasTexture(c)};
    img.src='images/bot1.png';
Custom sounds live in CUS.chase[v] and CUS.scare[v] (v = 0, 1, 2) as decoded AudioBuffers.
Example, call it after the first user tap:
    async function loadBundledAudio(){
      for(let v=0;v<3;v++){
        for(const k of ['chase','scare']){
          const r=await fetch('audio/'+k+(v+1)+'.mp3');
          CUS[k][v]=await ac.decodeAudioData(await r.arrayBuffer());
        }
      }
    }
Local files must be served over http(s) (see section 3, Option B), not opened with file://.
Default sounds are synthesized in code (array VAR and function scream(v)); no audio files are used.

## 8. Nextbot customization (search these in index.html)
- Speed: "s=5.8*AGGR*dt". 5.8 is the base speed in metres per second (player walks 5.2,
  sprints 8.5). Bot speed does not grow over time.
- Maximum bots: search "const want=". Story mode allows up to 3, endless mode 3.
  A new bot appears about every 45 seconds ("/45").
- Stun: 5 hits with gaps under 0.6 s freeze a bot for 10 s ("b.hits>=5", "b.stun=10", ">600").
- Jumpscares: function startScare, function scream(v) and the CSS classes .glitch .stat .inv.
- Hidden content: a secret level is triggered by the stun counter ("stunTotal>=6").

## 9. Common configuration points
- Player: walk 5.2 / sprint 8.5 / crouch 2.6 ("ws=crouch?"), jump "P.vy=7", gravity GRAV (20).
- Weapon: magazine "AMAX=15", starting spare magazines "reserve=2", max spare "Math.min(9,reserve+1)",
  fire rate "nextShot=now+160" (milliseconds).
- Levels: the THS array (story levels), plus CITY (endless mode) and ERR (error level).
  Each entry sets name, story text, fog colour and distance, brightness, wall colours and
  chunk weights ("w").
- World: chunks are 8x8 cells of 5 m ("G=8", "CS=5"). Loaded area: search "updChunks".
- Performance: lower "Math.min(devicePixelRatio,1.5)" in function rs, or reduce the loaded chunk area.

## 10. Known limitations
- Single-player only. The Multiplayer button is a placeholder.
- Performance depends on the device; mid-range phones may need the performance tweaks above.

## 11. Credits
Three.js (MIT). All default graphics and sounds are generated by code.