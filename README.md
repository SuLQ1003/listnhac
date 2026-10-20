<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8">
  <title>Offline Music Player</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; }
    body { background: #1e1e24; color: #fff; padding: 16px; display: flex; flex-direction: column; height: 100vh; }
    h3 { font-size: 16px; margin-bottom: 12px; color: #69b1ff; }
    .upload-btn { background: #333; border: 1px dashed #666; padding: 10px; border-radius: 6px; text-align: center; cursor: pointer; margin-bottom: 12px; }
    .upload-btn:hover { border-color: #69b1ff; }
    input[type="file"] { display: none; }
    .now-playing { font-size: 14px; font-weight: bold; margin-bottom: 10px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; color: #e6f7ff; }
    .controls { display: flex; align-items: center; justify-content: center; gap: 15px; margin-bottom: 16px; }
    button { background: #1677ff; color: #fff; border: none; padding: 8px 14px; border-radius: 6px; cursor: pointer; font-size: 14px; font-weight: bold; }
    button:hover { background: #4096ff; }
    .playlist { flex: 1; overflow-y: auto; background: #2b2b36; border-radius: 6px; padding: 8px; list-style: none; }
    .playlist li { padding: 8px 12px; font-size: 13px; border-radius: 4px; cursor: pointer; display: flex; justify-content: space-between; margin-bottom: 4px; }
    .playlist li:hover { background: #3c3c4d; }
    .playlist li.active { background: #1677ff; color: #fff; }
  </style>
</head>
<body>
  <h3>🎵 Trình phát nhạc Offline</h3>
  
  <label class="upload-btn" for="audioFiles">
    📂 Chọn các file nhạc hoặc thư mục nhạc từ máy
  </label>
  <input type="file" id="audioFiles" accept="audio/*" multiple webkitdirectory>

  <div class="now-playing" id="trackTitle">Chưa chọn bài hát</div>

  <div class="controls">
    <button onclick="prevTrack()">⏮ Trước</button>
    <button id="playBtn" onclick="togglePlay()">▶ Phát</button>
    <button onclick="nextTrack()">Sau ⏭</button>
  </div>

  <audio id="audioPlayer"></audio>
  <ul class="playlist" id="playlistView"></ul>

  <script>
    let playlist = [];
    let currentIndex = 0;
    const player = document.getElementById('audioPlayer');
    const trackTitle = document.getElementById('trackTitle');
    const playlistView = document.getElementById('playlistView');
    const playBtn = document.getElementById('playBtn');

    document.getElementById('audioFiles').addEventListener('change', function(e) {
      const files = Array.from(e.target.files).filter(f => f.type.startsWith('audio/') || f.name.match(/\.(mp3|wav|ogg|m4a|flac)$/i));
      if (!files.length) return;
      
      playlist = files.map(file => ({
        name: file.name.replace(/\.[^/.]+$/, ""),
        url: URL.createObjectURL(file)
      }));

      renderPlaylist();
      loadTrack(0);
    });

    function renderPlaylist() {
      playlistView.innerHTML = '';
      playlist.forEach((track, idx) => {
        const li = document.createElement('li');
        li.textContent = `${idx + 1}. ${track.name}`;
        li.onclick = () => loadTrack(idx, true);
        if (idx === currentIndex) li.classList.add('active');
        playlistView.appendChild(li);
      });
    }

    function loadTrack(index, autoPlay = false) {
      if (!playlist.length || index < 0 || index >= playlist.length) return;
      currentIndex = index;
      player.src = playlist[currentIndex].url;
      trackTitle.textContent = playlist[currentIndex].name;
      renderPlaylist();
      if (autoPlay) {
        player.play();
        playBtn.textContent = '⏸ Dừng';
      }
    }

    function togglePlay() {
      if (!playlist.length) return;
      if (player.paused) {
        player.play();
        playBtn.textContent = '⏸ Dừng';
      } else {
        player.pause();
        playBtn.textContent = '▶ Phát';
      }
    }

    function nextTrack() {
      if (!playlist.length) return;
      let nextIndex = (currentIndex + 1) % playlist.length;
      loadTrack(nextIndex, true);
    }

    function prevTrack() {
      if (!playlist.length) return;
      let prevIndex = (currentIndex - 1 + playlist.length) % playlist.length;
      loadTrack(prevIndex, true);
    }

    // Tự động chuyển bài khi hết nhạc
    player.onended = nextTrack;
  </script>
</body>
</html>
# listnhac1
