<template>
  <defs>
    <!-- Green metallic gradient for beer cap 3D effect -->
    <radialGradient id="greenMetallicGradient" cx="25%" cy="25%">
      <stop offset="0%" stop-color="#a5d6a7"/>
      <stop offset="30%" stop-color="#66bb6a"/>
      <stop offset="60%" stop-color="#4caf50"/>
      <stop offset="100%" stop-color="#2e7d32"/>
    </radialGradient>

    <!-- Inner circle metallic gradient -->
    <radialGradient id="innerGreenGradient" cx="30%" cy="30%">
      <stop offset="0%" stop-color="#81c784"/>
      <stop offset="50%" stop-color="#4caf50"/>
      <stop offset="100%" stop-color="#388e3c"/>
    </radialGradient>

    <!-- Highlight gradient for metallic shine -->
    <linearGradient id="metallicShine" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" stop-color="#c8e6c9" stop-opacity="0.8"/>
      <stop offset="50%" stop-color="#81c784" stop-opacity="0.4"/>
      <stop offset="100%" stop-color="#4caf50" stop-opacity="0.1"/>
    </linearGradient>

    <!-- Enhanced shadow filter for 3D effect -->
    <filter id="shadow3d">
      <feGaussianBlur in="SourceAlpha" stdDeviation="3"/>
      <feOffset dx="3" dy="3" result="offset"/>
      <feComponentTransfer>
        <feFuncA type="linear" slope="0.5"/>
      </feComponentTransfer>
      <feMerge>
        <feMergeNode/>
        <feMergeNode in="SourceGraphic"/>
      </feMerge>
    </filter>

    <!-- Emboss effect for metallic texture -->
    <filter id="emboss">
      <feConvolveMatrix kernelMatrix="
        -2 -1  0
        -1  1  1
         0  1  2"
                        order="3"
                        bias="0.5"
                        preserveAlpha="true"/>
    </filter>

    <!-- Red metallic gradient for reset arrow 3D effect -->
    <radialGradient id="redMetallicGradient" cx="25%" cy="25%">
      <stop offset="0%" stop-color="#ff7979"/>
      <stop offset="30%" stop-color="#e74c3c"/>
      <stop offset="60%" stop-color="#c0392b"/>
      <stop offset="100%" stop-color="#a93226"/>
    </radialGradient>

    <!-- Red arrow highlight gradient -->
    <linearGradient id="redArrowShine" x1="0%" y1="0%" x2="100%" y2="100%">
      <stop offset="0%" stop-color="#ff9999" stop-opacity="0.8"/>
      <stop offset="50%" stop-color="#ff6b6b" stop-opacity="0.4"/>
      <stop offset="100%" stop-color="#e74c3c" stop-opacity="0.1"/>
    </linearGradient>

    <!-- Skull gradients for 3D effect -->
    <radialGradient id="skullGradient" cx="30%" cy="30%">
      <stop offset="0%" stop-color="#ffffff"/>
      <stop offset="50%" stop-color="#f5f5f5"/>
      <stop offset="100%" stop-color="#e0e0e0"/>
    </radialGradient>

    <!-- Dark red prohibition circle gradient -->
    <radialGradient id="prohibitionGradient" cx="25%" cy="25%">
      <stop offset="0%" stop-color="#ff5252"/>
      <stop offset="50%" stop-color="#e53935"/>
      <stop offset="100%" stop-color="#c62828"/>
    </radialGradient>

    <!-- Gold metallic gradients for casino chest -->
    <radialGradient id="goldGradient" cx="25%" cy="25%">
      <stop offset="0%" stop-color="#fff9c4"/>
      <stop offset="20%" stop-color="#ffd700"/>
      <stop offset="60%" stop-color="#ffb300"/>
      <stop offset="100%" stop-color="#b8860b"/>
    </radialGradient>

    <!-- Dark gold for chest shadows -->
    <radialGradient id="darkGoldGradient" cx="30%" cy="30%">
      <stop offset="0%" stop-color="#cd853f"/>
      <stop offset="50%" stop-color="#b8860b"/>
      <stop offset="100%" stop-color="#8b6914"/>
    </radialGradient>

    <!-- Casino red velvet interior -->
    <radialGradient id="redVelvetGradient" cx="50%" cy="30%">
      <stop offset="0%" stop-color="#ff6b6b"/>
      <stop offset="50%" stop-color="#dc143c"/>
      <stop offset="100%" stop-color="#8b0000"/>
    </radialGradient>

    <!-- Jewel sparkle gradient -->
    <radialGradient id="jewelGradient" cx="30%" cy="30%">
      <stop offset="0%" stop-color="#ffffff"/>
      <stop offset="30%" stop-color="#00ff88"/>
      <stop offset="60%" stop-color="#0080ff"/>
      <stop offset="100%" stop-color="#8000ff"/>
    </radialGradient>
  </defs>
</template>