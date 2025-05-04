# Rebel-Fitness-World
AI Powered Gym Application
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>AI-Powered Gym App</title>
  <script type="module">
    import * as THREE from 'https://cdn.jsdelivr.net/npm/three@0.153.0/build/three.module.js';
    window.THREE = THREE;
  </script>
  <style>
    body { margin: 0; font-family: Arial, sans-serif; }
    #three-canvas { position: absolute; top: 0; left: 0; z-index: -1; }
    .min-h-screen { min-height: 100vh; }
    .bg-gray-100 { background-color: #f3f4f6; }
    .p-4 { padding: 1rem; }
    .flex { display: flex; }
    .justify-center { justify-content: center; }
    .items-center { align-items: center; }
    .h-screen { height: 100vh; }
    .bg-blue-500 { background-color: #3b82f6; }
    .bg-green-500 { background-color: #22c55e; }
    .bg-purple-500 { background-color: #a855f7; }
    .bg-spotify { background-color: #1db954; }
    .text-white { color: #ffffff; }
    .px-4 { padding-left: 1rem; padding-right: 1rem; }
    .py-2 { padding-top: 0.5rem; padding-bottom: 0.5rem; }
    .rounded { border-radius: 0.25rem; }
    .hover\:bg-blue-600:hover { background-color: #2563eb; }
    .hover\:bg-green-600:hover { background-color: #16a34a; }
    .hover\:bg-purple-600:hover { background-color: #9333ea; }
    .hover\:bg-spotify:hover { background-color: #1aa34a; }
    .max-w-4xl { max-width: 56rem; }
    .mx-auto { margin-left: auto; margin-right: auto; }
    .text-3xl { font-size: 1.875rem; }
    .text-xl { font-size: 1.25rem; }
    .font-bold { font-weight: 700; }
    .mb-4 { margin-bottom: 1rem; }
    .mb-2 { margin-bottom: 0.5rem; }
    .mt-2 { margin-top: 0.5rem; }
    .border { border: 1px solid #e5e7eb; }
    .p-2 { padding: 0.5rem; }
    .h-40 { height: 10rem; }
    .overflow-y-auto { overflow-y: auto; }
    .w-full { width: 100%; }
    .grid { display: grid; }
    .grid-cols-2 { grid-template-columns: repeat(2, minmax(0, 1fr)); }
    .gap-4 { gap: 1rem; }
  </style>
</head>
<body>
  <div id="root"></div>
  <script type="module">
    import React, { useState, useEffect } from 'https://esm.sh/react@18.2.0';
    import ReactDOM from 'https://esm.sh/react-dom@18.2.0';

    // Mock WebSocket for real-time features
    const mockSocket = {
      send: (data) => {
        const parsed = JSON.parse(data);
        mockSocket.onmessage({ data: JSON.stringify(parsed) });
      },
      onmessage: () => {},
    };

    // Mock biometric data
    const mockBiometrics = { heartRate: 75, steps: 1000 };

    // AI Companion Options
    const aiCompanions = [
      { id: 'ai1', name: 'Coach Alpha', style: 'Energetic', color: 0xff0000 },
      { id: 'ai2', name: 'Trainer Beta', style: 'Calm', color: 0x00ff00 },
      { id: 'ai3', name: 'Mentor Gamma', style: 'Motivational', color: 0x0000ff },
    ];

    // Mock Spotify Component
    function SpotifyPlayer({ onSongChange }) {
      const [currentTrack, setCurrentTrack] = useState(null);

      const playSong = (trackName) => {
        setCurrentTrack(trackName);
        onSongChange(trackName);
      };

      return React.createElement(
        'div',
        { className: 'mb-4' },
        React.createElement('h2', { className: 'text-xl' }, 'Spotify Player'),
        React.createElement('p', null, `Now Playing: ${currentTrack || 'None'}`),
        React.createElement(
          'button',
          {
            className: 'bg-spotify text-white px-4 py-2 rounded hover:bg-spotify mt-2',
            onClick: () => playSong('Sample Track'),
          },
          'Play Sample Track'
        )
      );
    }

    // Main App Component
    function App() {
      const [user, setUser] = useState(null);
      const [chatMessages, setChatMessages] = useState([]);
      const [playlist, setPlaylist] = useState([]);
      const [activeSong, setActiveSong] = useState(null);
      const [message, setMessage] = useState('');
      const [selectedAI, setSelectedAI] = useState(aiCompanions[0]);
      const [spotifyEnabled, setSpotifyEnabled] = useState(false);

      // Mock WebSocket event handlers
      useEffect(() => {
        mockSocket.onmessage = (event) => {
          const data = JSON.parse(event.data);
          if (data.type === 'chat') {
            setChatMessages((prev) => [...prev, data.message]);
          } else if (data.type === 'song') {
            setActiveSong(data.song);
          }
        };
      }, []);

      // Mock login
      const handleLogin = () => {
        setUser({ id: 'user1', name: 'John Doe' });
        setSpotifyEnabled(true);
      };

      // Send chat message
      const sendMessage = () => {
        if (message) {
          mockSocket.send(JSON.stringify({ type: 'chat', message: `${user.name}: ${message}` }));
          setMessage('');
        }
      };

      // Add song to playlist
      const addSong = (song) => {
        setPlaylist((prev) => [...prev, song]);
        mockSocket.send(JSON.stringify({ type: 'song', song }));
      };

      // Select AI companion
      const selectAI = (ai) => {
        setSelectedAI(ai);
      };

      // Three.js for 3D SI and AI Companion
      useEffect(() => {
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ canvas: document.getElementById('three-canvas') });
        renderer.setSize(window.innerWidth, window.innerHeight);

        // Central SI model
        const siGeometry = new THREE.SphereGeometry(1.5, 32, 32);
        const siMaterial = new THREE.MeshBasicMaterial({ color: 0xffff00 });
        const siModel = new THREE.Mesh(siGeometry, siMaterial);
        siModel.position.set(0, 0, 0);
        scene.add(siModel);

        // Personal AI Companion model
        const aiGeometry = new THREE.BoxGeometry(0.5, 0.5, 0.5);
        const aiMaterial = new THREE.MeshBasicMaterial({ color: selectedAI.color });
        const aiModel = new THREE.Mesh(aiGeometry, aiMaterial);
        aiModel.position.set(2, 0, 0);
        scene.add(aiModel);

        camera.position.z = 5;

        const animate = () => {
          requestAnimationFrame(animate);
          siModel.rotation.y += 0.01;
          aiModel.rotation.y += 0.02;
          renderer.render(scene, camera);
        };
        animate();

        return () => renderer.dispose();
      }, [selectedAI]);

      return React.createElement(
        'div',
        { className: 'min-h-screen bg-gray-100 p-4' },
        React.createElement('canvas', { id: 'three-canvas' }),
        !user
          ? React.createElement(
              'div',
              { className: 'flex justify-center items-center h-screen' },
              React.createElement(
                'button',
                {
                  className: 'bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600',
                  onClick: handleLogin,
                },
                'Login'
              )
            )
          : React.createElement(
              'div',
              { className: 'max-w-4xl mx-auto' },
              React.createElement('h1', { className: 'text-3xl font-bold mb-4' }, 'AI-Powered Gym'),
              React.createElement(
                'div',
                { className: 'mb-4' },
                React.createElement('h2', { className: 'text-xl' }, 'Super Intelligence Hologram'),
                React.createElement(
                  'p',
                  null,
                  `Central AI guiding your workout: ${mockBiometrics.heartRate} BPM, ${mockBiometrics.steps} steps`
                )
              ),
              React.createElement(
                'div',
                { className: 'mb-4' },
                React.createElement('h2', { className: 'text-xl' }, 'Personal AI Companion'),
                React.createElement(
                  'p',
                  null,
                  `${selectedAI.name} (${selectedAI.style}): "Keep pushing, ${user.name}!"`
                ),
                React.createElement(
                  'div',
                  { className: 'grid grid-cols-2 gap-4 mt-2' },
                  aiCompanions.map((ai) =>
                    React.createElement(
                      'button',
                      {
                        key: ai.id,
                        className: `bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600 ${
                          selectedAI.id === ai.id ? 'bg-blue-700' : ''
                        }`,
                        onClick: () => selectAI(ai),
                      },
                      ai.name
                    )
                  )
                )
              ),
              React.createElement(
                'div',
                { className: 'mb-4' },
                React.createElement('h2', { className: 'text-xl' }, 'Chat'),
                React.createElement(
                  'div',
                  { className: 'border p-2 h-40 overflow-y-auto mb-2' },
                  chatMessages.map((msg, index) => React.createElement('p', { key: index }, msg))
                ),
                React.createElement('input', {
                  type: 'text',
                  value: message,
                  onChange: (e) => setMessage(e.target.value),
                  className: 'border p-2 w-full',
                  placeholder: 'Type a message...',
                }),
                React.createElement(
                  'button',
                  {
                    className: 'bg-green-500 text-white px-4 py-2 rounded mt-2 hover:bg-green-600',
                    onClick: sendMessage,
                  },
                  'Send'
                )
              ),
              React.createElement(
                'div',
                { className: 'mb-4' },
                React.createElement('h2', { className: 'text-xl' }, 'Music Playlist'),
                React.createElement(
                  'button',
                  {
                    className: 'bg-purple-500 text-white px-4 py-2 rounded mb-2 hover:bg-purple-600',
                    onClick: () => addSong(`Song ${playlist.length + 1}`),
                  },
                  'Add Custom Song'
                ),
                React.createElement('p', null, `Now Playing: ${activeSong || 'None'}`),
                React.createElement(
                  'ul',
                  null,
                  playlist.map((song, index) => React.createElement('li', { key: index }, song))
                )
              ),
              spotifyEnabled && React.createElement(SpotifyPlayer, { onSongChange: setActiveSong })
            )
      );
    }

    // Render App with createRoot
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(React.createElement(App));
  </script>
</body>
</html>
