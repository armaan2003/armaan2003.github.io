<html>
<head>
<meta charset="UTF-8">
<title>FrontierSat Orbit Viewer</title>
<style>
  body { margin: 0; background: #111; color: #ddd; font-family: monospace; }
  #panel { position: fixed; top: 10px; left: 10px; width: 300px; background: #000c; padding: 10px; border: 1px solid #555; }
  #panel label { display: block; margin-top: 6px; font-size: 12px; }
  #panel input { width: 100%; background: #111; color: #ddd; border: 1px solid #555; padding: 3px; box-sizing: border-box; }
  #panel button { width: 100%; margin-top: 8px; padding: 8px; background: #246; color: #fff; border: none; }
  #log { white-space: pre-wrap; font-size: 12px; margin-top: 8px; max-height: 220px; overflow-y: auto; border-top: 1px solid #555; padding-top: 6px; }
  canvas { display: block; }
  #hoverInfo { position: fixed; bottom: 10px; left: 50%; transform: translateX(-50%); background: #000c; padding: 6px 14px; border: 1px solid #555; font-size: 13px; }
  #liveInfo { position: fixed; top: 10px; right: 10px; background: #000c; padding: 8px 12px; border: 1px solid #555; font-size: 13px; text-align: right; }
  .label3d { position: fixed; color: #fff; font-size: 12px; padding: 2px 6px; background: #000a; border-radius: 3px; pointer-events: none; white-space: nowrap; transform: translate(10px, -50%); }
</style>
</head>
<body>

<div id="hoverInfo">Hover the globe for lat / lon</div>
<div id="liveInfo"></div>
<div id="satLabel" class="label3d">FrontierSat</div>
<div id="gsLabel" class="label3d"></div>

<div id="panel">
  <label>Time (UTC), blank = now:</label>
  <input id="queryTime" placeholder="2026-09-02 14:30:00">

  <label>Time window (hours):</label>
  <input id="playHours" value="3">

  <label>Ground station name:</label>
  <input id="gsName" value="Rothney Astrophysical Observatory">

  <label>Latitude:</label>
  <input id="gsLat" value="50.8684">

  <label>Longitude:</label>
  <input id="gsLon" value="-114.2910">

  <label>Altitude (m):</label>
  <input id="gsAlt" value="1269">

  <label>Min elevation (deg):</label>
  <input id="minElev" value="10">

  <label>Space-Track username (optional, for far dates):</label>
  <input id="stUser">

  <label>Space-Track password (plain text, not hidden):</label>
  <input id="stPass">

  <button onclick="run()">Show Orbit</button>
  <div id="log">Ready.</div>
</div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/satellite.js@5.0.0/dist/satellite.min.js"></script>

<script>
'use strict';

const NORAD_ID = 69015;
const FAR_DATE_DAYS = 10;
const EARTH_RADIUS_KM = 6378.137;
const SCALE = 1 / 1000;

// backup TLE, saved 2026-09-03 -- replace if it gets too old
const BACKUP_TLE = [
  '1 69015U 26100AM  26245.23204559  .00004518  00000-0  19621-3 0  9999',
  '2 69015  97.3884 141.8327 0009498 124.5280 235.6855 15.22777030 18546',
];

function log(msg) {
  document.getElementById('log').textContent += '\n' + msg;
}

function parseTleEpoch(line1) {
  const parts = line1.trim().split(/\s+/);
  const epochText = parts[3];
  let year = parseInt(epochText.slice(0, 2), 10);
  year = year < 57 ? 2000 + year : 1900 + year;
  const dayOfYear = parseFloat(epochText.slice(2));
  return new Date(Date.UTC(year, 0, 1) + (dayOfYear - 1) * 86400000);
}

async function getLatestTle() {
  try {
    const url = `https://celestrak.org/NORAD/elements/gp.php?CATNR=${NORAD_ID}&FORMAT=TLE`;
    const text = (await (await fetch(url)).text()).trim().split('\n').map(s => s.trim()).filter(Boolean);
    const line1 = text[text.length - 2], line2 = text[text.length - 1];
    if (!line1.startsWith('1 ') || !line2.startsWith('2 ')) throw new Error('bad format');
    return { line1, line2, source: 'CelesTrak (live)' };
  } catch (e) {
    return { line1: BACKUP_TLE[0], line2: BACKUP_TLE[1], source: 'saved backup (2026-09-03)' };
  }
}

async function spacetrackQuery(queryUrl, username, password) {
  const body = new URLSearchParams({ identity: username, password: password, query: queryUrl });
  const resp = await fetch('https://www.space-track.org/ajaxauth/login', { method: 'POST', body });
  if (!resp.ok) throw new Error('Space-Track request failed: ' + resp.status);
  return await resp.text();
}

async function getTleFromSpacetrack(targetDate, username, password) {
  const windowDays = 4;
  const d0 = new Date(targetDate.getTime() - windowDays * 86400000).toISOString().slice(0, 10);
  const d1 = new Date(targetDate.getTime() + windowDays * 86400000).toISOString().slice(0, 10);
  const url = `https://www.space-track.org/basicspacedata/query/class/gp_history/NORAD_CAT_ID/${NORAD_ID}/EPOCH/${d0}--${d1}/orderby/EPOCH%20asc/format/tle`;
  const raw = await spacetrackQuery(url, username, password);
  const lines = raw.trim().split('\n').map(s => s.trim()).filter(Boolean);
  const numTles = Math.floor(lines.length / 2);
  if (numTles < 1) throw new Error('no TLEs found for that date range');

  let bestDiff = Infinity, line1 = '', line2 = '';
  for (let k = 0; k < numTles; k++) {
    const l1 = lines[2*k], l2 = lines[2*k + 1];
    const diffDays = Math.abs(targetDate - parseTleEpoch(l1)) / 86400000;
    if (diffDays < bestDiff) { bestDiff = diffDays; line1 = l1; line2 = l2; }
  }
  return { line1, line2, source: `Space-Track (${bestDiff.toFixed(1)} days from target)` };
}

async function getMeanMotionTrend(username, password) {
  const now = new Date();
  const d0 = new Date(now.getTime() - 21 * 86400000).toISOString().slice(0, 10);
  const d1 = now.toISOString().slice(0, 10);
  const url = `https://www.space-track.org/basicspacedata/query/class/gp_history/NORAD_CAT_ID/${NORAD_ID}/EPOCH/${d0}--${d1}/orderby/EPOCH%20asc/format/tle`;
  const raw = await spacetrackQuery(url, username, password);
  const lines = raw.trim().split('\n').map(s => s.trim()).filter(Boolean);
  const numTles = Math.floor(lines.length / 2);
  if (numTles < 3) throw new Error('not enough recent TLEs for a trend');

  const firstEpoch = parseTleEpoch(lines[0]);
  const daysElapsed = [], meanMotions = [];
  for (let k = 0; k < numTles; k++) {
    daysElapsed.push((parseTleEpoch(lines[2*k]) - firstEpoch) / 86400000);
    meanMotions.push(parseFloat(lines[2*k + 1].trim().split(/\s+/)[7]));
  }
  const n = numTles;
  const sumX = daysElapsed.reduce((a,b) => a+b, 0);
  const sumY = meanMotions.reduce((a,b) => a+b, 0);
  const sumXY = daysElapsed.reduce((s,x,i) => s + x*meanMotions[i], 0);
  const sumXX = daysElapsed.reduce((s,x) => s + x*x, 0);
  const trendPerDay = (n*sumXY - sumX*sumY) / (n*sumXX - sumX*sumX);
  return { trendPerDay, numTles };
}

function computePasses(satrec, startTime, endTime, sampleSeconds, observerGd, minElevDeg) {
  const passes = [];
  const minElevRad = satellite.degreesToRadians(minElevDeg);
  let inPass = false, passStart = null;
  for (let t = startTime.getTime(); t <= endTime.getTime(); t += sampleSeconds * 1000) {
    const date = new Date(t);
    const pv = satellite.propagate(satrec, date);
    if (!pv || !pv.position) continue;
    const gmst = satellite.gstime(date);
    const positionEcf = satellite.eciToEcf(pv.position, gmst);
    const look = satellite.ecfToLookAngles(observerGd, positionEcf);
    if (look.elevation > minElevRad) {
      if (!inPass) { inPass = true; passStart = date; }
    } else if (inPass) {
      passes.push({ start: passStart, end: date });
      inPass = false;
    }
  }
  if (inPass) passes.push({ start: passStart, end: new Date(endTime.getTime()) });
  return passes;
}

async function run() {
  document.getElementById('log').textContent = 'Working...';

  const queryTime = document.getElementById('queryTime').value.trim();
  const playHours = parseFloat(document.getElementById('playHours').value);
  const gsName = document.getElementById('gsName').value;
  const gsLat = parseFloat(document.getElementById('gsLat').value);
  const gsLon = parseFloat(document.getElementById('gsLon').value);
  const gsAlt = parseFloat(document.getElementById('gsAlt').value);
  const minElev = parseFloat(document.getElementById('minElev').value);
  const username = document.getElementById('stUser').value.trim();
  const password = document.getElementById('stPass').value;
  const haveLogin = username && password;

  const startTime = queryTime ? new Date(queryTime.replace(' ', 'T') + 'Z') : new Date();
  const endTime = new Date(startTime.getTime() + playHours * 3600000);
  const daysAway = (startTime - new Date()) / 86400000;

  let tle1 = '', tle2 = '', tleSource = '';

  if (haveLogin && Math.abs(daysAway) > FAR_DATE_DAYS) {
    try {
      ({ line1: tle1, line2: tle2, source: tleSource } = await getTleFromSpacetrack(startTime, username, password));
    } catch (e) {
      // no historical TLE, likely a future date -- falls back below
    }
  }

  if (!tle1) {
    ({ line1: tle1, line2: tle2, source: tleSource } = await getLatestTle());
    if (haveLogin && daysAway > FAR_DATE_DAYS) {
      try {
        const { trendPerDay, numTles } = await getMeanMotionTrend(username, password);
        log(`Recent trend: mean motion changing ${trendPerDay.toFixed(6)}/day (last ${numTles} TLEs).`);
      } catch (e) {
        // trend check is a nice-to-have, ok to skip if it fails
      }
    }
  }

  log('TLE source: ' + tleSource);

  const tleEpoch = parseTleEpoch(tle1);
  const tleAgeDays = (startTime - tleEpoch) / 86400000;
  if (Math.abs(tleAgeDays) > FAR_DATE_DAYS) {
    log(`Note: TLE is ${tleAgeDays.toFixed(0)} days from your requested time, may not be accurate.`);
  }

  const satrec = satellite.twoline2satrec(tle1, tle2);

  const meanMotion = parseFloat(tle2.trim().split(/\s+/)[7]);
  const periodMinutes = 1440 / meanMotion;

  const gmstNow = satellite.gstime(startTime);
  const pvNow = satellite.propagate(satrec, startTime);
  const geoNow = satellite.eciToGeodetic(pvNow.position, gmstNow);
  log(`Orbit period: ${periodMinutes.toFixed(1)} min`);

  const observerGd = {
    latitude: satellite.degreesToRadians(gsLat),
    longitude: satellite.degreesToRadians(gsLon),
    height: gsAlt / 1000,
  };
  const passes = computePasses(satrec, startTime, endTime, 10, observerGd, minElev);
  log(`\n${gsName} (${playHours}h window):`);
  if (passes.length === 0) {
    log('  No passes.');
  } else {
    passes.forEach((p, i) => {
      const durationMin = (p.end - p.start) / 60000;
      const day = p.start.toISOString().slice(5,10);
      const t0 = p.start.toISOString().slice(11,16);
      const t1 = p.end.toISOString().slice(11,16);
      log(`  ${day} ${t0}\u2013${t1}  (${durationMin.toFixed(1)} min)`);
    });
  }

  showGlobe(satrec, startTime, endTime, periodMinutes, observerGd);
}

// ---------- 3D view ----------

let scene, camera, renderer, orbitLine, groundTrackLine, satMarker, gsMarker;
let liveSatrec = null, liveStartTime = null, liveEndTime = null, liveObserverGd = null, liveRealStart = null;
const PLAYBACK_SPEED = 60; // sim seconds per real second, matches the MATLAB version

function initScene() {
  scene = new THREE.Scene();
  camera = new THREE.PerspectiveCamera(50, window.innerWidth / window.innerHeight, 0.1, 1000);
  camera.position.set(0, 5, 17);

  renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);
  renderer.setPixelRatio(window.devicePixelRatio);

  scene.add(new THREE.AmbientLight(0xffffff, 1.0));

  const earthRadius = EARTH_RADIUS_KM * SCALE;
  const geo = new THREE.SphereGeometry(earthRadius, 96, 96);
  const mat = new THREE.MeshPhongMaterial({ color: 0x2255aa, shininess: 5 });
  const earth = new THREE.Mesh(geo, mat);
  earth.name = 'earth';
  scene.add(earth);

  new THREE.TextureLoader().load(
    'https://cdn.jsdelivr.net/gh/mrdoob/three.js@r128/examples/textures/planets/earth_atmos_2048.jpg',
    (tex) => {
      tex.anisotropy = renderer.capabilities.getMaxAnisotropy();
      tex.minFilter = THREE.LinearMipmapLinearFilter;
      mat.map = tex;
      mat.color.set(0xffffff);
      mat.needsUpdate = true;
    }
  );

  orbitLine = new THREE.Line(new THREE.BufferGeometry(), new THREE.LineBasicMaterial({ color: 0xffa64d }));
  scene.add(orbitLine);

  groundTrackLine = new THREE.Line(new THREE.BufferGeometry(), new THREE.LineDashedMaterial({ color: 0x4da6ff, dashSize: 0.15, gapSize: 0.1 }));
  scene.add(groundTrackLine);

  satMarker = new THREE.Mesh(new THREE.SphereGeometry(0.09, 12, 12), new THREE.MeshBasicMaterial({ color: 0xff4d4d }));
  scene.add(satMarker);

  gsMarker = new THREE.Mesh(new THREE.SphereGeometry(0.07, 12, 12), new THREE.MeshBasicMaterial({ color: 0x4dff4d }));
  scene.add(gsMarker);

  window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
    renderer.setSize(window.innerWidth, window.innerHeight);
  });

  setupCameraControls();
  setupHover(earth);
  animate();
}

// Plain drag-to-rotate, scroll-to-zoom. No external controls library --
// nothing here that can fail to load.
let camDistance = 17;
let camTheta = 0;
let camPhi = 1.2;

function updateCameraPosition() {
  camera.position.set(
    camDistance * Math.sin(camPhi) * Math.cos(camTheta),
    camDistance * Math.cos(camPhi),
    camDistance * Math.sin(camPhi) * Math.sin(camTheta)
  );
  camera.lookAt(0, 0, 0);
}

function setupCameraControls() {
  const dom = renderer.domElement;
  let dragging = false;
  let lastX = 0;
  let lastY = 0;

  dom.addEventListener('mousedown', (e) => { dragging = true; lastX = e.clientX; lastY = e.clientY; });
  window.addEventListener('mouseup', () => { dragging = false; });
  dom.addEventListener('mousemove', (e) => {
    if (!dragging) return;
    camTheta += (e.clientX - lastX) * 0.005;
    camPhi -= (e.clientY - lastY) * 0.005;
    camPhi = Math.max(0.1, Math.min(Math.PI - 0.1, camPhi));
    lastX = e.clientX;
    lastY = e.clientY;
    updateCameraPosition();
  });
  dom.addEventListener('wheel', (e) => {
    e.preventDefault();
    camDistance = Math.max(8, Math.min(50, camDistance + e.deltaY * 0.01));
    updateCameraPosition();
  }, { passive: false });

  updateCameraPosition();
}

function animate() {
  requestAnimationFrame(animate);
  if (liveSatrec) updateSatellitePosition();
  renderer.render(scene, camera);
}

function projectToScreen(worldPos, labelEl) {
  const v = worldPos.clone().project(camera);
  if (v.z > 1) { labelEl.style.display = 'none'; return; }
  labelEl.style.display = 'block';
  labelEl.style.left = ((v.x + 1) / 2 * window.innerWidth) + 'px';
  labelEl.style.top = ((1 - v.y) / 2 * window.innerHeight) + 'px';
}

function updateSatellitePosition() {
  const elapsedReal = (Date.now() - liveRealStart) / 1000; // seconds since play started
  let simTime = new Date(liveStartTime.getTime() + elapsedReal * PLAYBACK_SPEED * 1000);
  const windowMs = liveEndTime - liveStartTime;
  if (windowMs > 0) {
    const loopedMs = ((simTime - liveStartTime) % windowMs + windowMs) % windowMs;
    simTime = new Date(liveStartTime.getTime() + loopedMs);
  }

  const pv = satellite.propagate(liveSatrec, simTime);
  if (!pv || !pv.position) return;
  satMarker.position.copy(eciToScene(pv.position));

  const gmst = satellite.gstime(simTime);
  const gsEcf = satellite.geodeticToEcf(liveObserverGd);
  gsMarker.position.copy(eciToScene(ecfToEci(gsEcf, gmst)));

  projectToScreen(satMarker.position, document.getElementById('satLabel'));
  projectToScreen(gsMarker.position, document.getElementById('gsLabel'));

  const geo = satellite.eciToGeodetic(pv.position, gmst);
  const speed = Math.sqrt(pv.velocity.x**2 + pv.velocity.y**2 + pv.velocity.z**2);
  document.getElementById('liveInfo').innerHTML =
    `${simTime.toISOString().slice(0,19).replace('T',' ')} UTC<br>` +
    `lat ${satellite.radiansToDegrees(geo.latitude).toFixed(2)}, lon ${satellite.radiansToDegrees(geo.longitude).toFixed(2)}<br>` +
    `alt ${geo.height.toFixed(0)} km, speed ${speed.toFixed(2)} km/s`;
}

function setupHover(earthMesh) {
  const raycaster = new THREE.Raycaster();
  const mouseNDC = new THREE.Vector2();
  const hoverEl = document.getElementById('hoverInfo');

  renderer.domElement.addEventListener('mousemove', (e) => {
    mouseNDC.x = (e.clientX / window.innerWidth) * 2 - 1;
    mouseNDC.y = -(e.clientY / window.innerHeight) * 2 + 1;
    raycaster.setFromCamera(mouseNDC, camera);
    const hits = raycaster.intersectObject(earthMesh);
    if (hits.length === 0) {
      hoverEl.textContent = 'Hover the globe for lat / lon';
      return;
    }
    const u = hits[0].uv.x, v = hits[0].uv.y;
    const lon = ((u - 0.5) * 360 + 540) % 360 - 180;
    const lat = (0.5 - v) * 180;
    hoverEl.textContent = `Lat ${lat.toFixed(2)}°, Lon ${lon.toFixed(2)}°`;
  });
}

function eciToScene(p) {
  return new THREE.Vector3(p.x * SCALE, p.z * SCALE, -p.y * SCALE);
}

function ecfToEci(posEcf, gmst) {
  // inverse of satellite.js's eciToEcf: rotate back by -gmst
  return {
    x: posEcf.x * Math.cos(gmst) - posEcf.y * Math.sin(gmst),
    y: posEcf.x * Math.sin(gmst) + posEcf.y * Math.cos(gmst),
    z: posEcf.z,
  };
}

function showGlobe(satrec, startTime, endTime, periodMinutes, observerGd) {
  const points = [];
  const groundPoints = [];
  const earthRadius = EARTH_RADIUS_KM;
  const n = 200;
  for (let i = 0; i < n; i++) {
    const t = new Date(startTime.getTime() - (periodMinutes/2)*60000 + (periodMinutes*60000*i)/(n-1));
    const pv = satellite.propagate(satrec, t);
    if (!pv || !pv.position) continue;
    points.push(eciToScene(pv.position));

    // ground track: same direction as the satellite, but pulled down to Earth's surface
    const r = Math.sqrt(pv.position.x**2 + pv.position.y**2 + pv.position.z**2);
    const surfacePos = { x: pv.position.x * earthRadius / r, y: pv.position.y * earthRadius / r, z: pv.position.z * earthRadius / r };
    groundPoints.push(eciToScene(surfacePos));
  }
  orbitLine.geometry.dispose();
  orbitLine.geometry = new THREE.BufferGeometry().setFromPoints(points);

  groundTrackLine.geometry.dispose();
  groundTrackLine.geometry = new THREE.BufferGeometry().setFromPoints(groundPoints);
  groundTrackLine.computeLineDistances(); // required for dashed lines to render correctly

  liveSatrec = satrec;
  liveStartTime = startTime;
  liveEndTime = endTime;
  liveObserverGd = observerGd;
  liveRealStart = Date.now();

  log(`\nAnimating live (${PLAYBACK_SPEED}x speed, loops every ${((endTime-startTime)/3600000).toFixed(1)}h).`);
  log('Orange = orbit, blue dashed = ground track. Drag/scroll to navigate.');
  document.getElementById('gsLabel').textContent = document.getElementById('gsName').value;
}

initScene();
</script>
</body>
</html>
