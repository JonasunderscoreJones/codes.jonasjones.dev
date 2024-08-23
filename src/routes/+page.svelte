<script>
    import bwipjs from 'bwip-js';
  
    let text = '';
    let qrType = 'text'; // QR code specific type
    let imageDataUrl = '';
    let activeTab = 'qr'; // Main tab (QR, Barcode, Data Matrix)
    let activeQRType = 'text'; // Sub-tab for QR code types
  
    // Variables for specific QR code types
    let ssid = '';
    let password = '';
    let encryption = 'WPA';
    let lat = '';
    let long = '';
    let name = '';
    let phone = '';
    let email = '';
  
    // Format data based on selected QR code type
    function formatQRCodeData() {
      switch (activeQRType) {
        case 'url':
          return text;
        case 'wifi':
          return `WIFI:T:${encryption};S:${ssid};P:${password};;`;
        case 'location':
          return `geo:${lat},${long}`;
        case 'contact':
          return `MECARD:N:${name};TEL:${phone};EMAIL:${email};;`;
        case 'phone':
          return `tel:${phone}`;
        case 'email':
          return `mailto:${email}`;
        case 'sms':
          return `smsto:${phone}:${text}`;
        case 'otp':
          return `otpauth://totp/Service:${text}?secret=ABC123&issuer=Service`;
        case 'mms':
          return `mmsto:${phone}:${text}`;
        case 'bitcoin':
          return `bitcoin:${text}`;
        case 'bookmark':
          return `MEBKM:TITLE:${text};URL:${text};;`;
        case 'app':
          return `market://details?id=${text}`;
        case 'event':
          return `BEGIN:VEVENT\nSUMMARY:${text}\nDTSTART:20220101T090000\nEND:VEVENT`;
        default:
          return text;  // Default is plain text
      }
    }
  
    async function generateCode() {
      const canvas = document.createElement('canvas');
      try {
        const formattedData = formatQRCodeData();
  
        if (activeTab === 'qr') {
          // Generate QR code using bwip-js
          bwipjs.toCanvas(canvas, {
            bcid: 'qrcode',
            text: formattedData,
            scale: 5,
            includetext: false,
          });
        } else if (activeTab === 'datamatrix') {
          // Generate Data Matrix code using bwip-js
          bwipjs.toCanvas(canvas, {
            bcid: 'datamatrix',
            text: text,
            scale: 3,
            includetext: false,
          });
        } else if (activeTab === '2dbarcode') {
          // Generate Code128 barcode using bwip-js
          bwipjs.toCanvas(canvas, {
            bcid: 'code128',
            text: text,
            scale: 3,
            includetext: false,
          });
        }
  
        imageDataUrl = canvas.toDataURL();
      } catch (err) {
        console.error(err);
      }
    }
  
    function downloadPng() {
      if (!imageDataUrl) return;
  
      const link = document.createElement('a');
      link.href = imageDataUrl;
      link.download = `${activeTab}_code.png`;
      link.click();
    }
  </script>
  
  <style>
    img {
      max-width: 100%;
      height: auto;
      margin-top: 20px;
    }
  
    .tabs, .sub-tabs {
      display: flex;
      margin-bottom: 20px;
      border-bottom: 2px solid #ddd;
    }
  
    .tab, .sub-tab {
      padding: 10px 20px;
      cursor: pointer;
      border-bottom: 2px solid transparent;
      margin-right: 10px;
    }
  
    .tab.active, .sub-tab.active {
      border-bottom: 2px solid #007acc;
      font-weight: bold;
    }
  
    .form-fields {
      margin-top: 20px;
    }
  </style>
  
  <main>
    <h1>Barcode & QR Code Generator</h1>
  
    <!-- Main Tabs for QR, Barcode, Data Matrix -->
    <div class="tabs">
      <div class="tab {activeTab === 'qr' ? 'active' : ''}" on:click={() => activeTab = 'qr'}>QR Code</div>
      <div class="tab {activeTab === 'datamatrix' ? 'active' : ''}" on:click={() => activeTab = 'datamatrix'}>Data Matrix</div>
      <div class="tab {activeTab === '2dbarcode' ? 'active' : ''}" on:click={() => activeTab = '2dbarcode'}>2D Barcode</div>
    </div>
  
    {#if activeTab === 'qr'}
      <!-- Sub-tabs for QR Code Types -->
      <div class="sub-tabs">
        <div class="sub-tab {activeQRType === 'text' ? 'active' : ''}" on:click={() => activeQRType = 'text'}>Text</div>
        <div class="sub-tab {activeQRType === 'url' ? 'active' : ''}" on:click={() => activeQRType = 'url'}>URL</div>
        <div class="sub-tab {activeQRType === 'wifi' ? 'active' : ''}" on:click={() => activeQRType = 'wifi'}>Wi-Fi</div>
        <div class="sub-tab {activeQRType === 'location' ? 'active' : ''}" on:click={() => activeQRType = 'location'}>Location</div>
        <div class="sub-tab {activeQRType === 'contact' ? 'active' : ''}" on:click={() => activeQRType = 'contact'}>Contact</div>
        <div class="sub-tab {activeQRType === 'phone' ? 'active' : ''}" on:click={() => activeQRType = 'phone'}>Phone</div>
        <div class="sub-tab {activeQRType === 'email' ? 'active' : ''}" on:click={() => activeQRType = 'email'}>Email</div>
        <div class="sub-tab {activeQRType === 'sms' ? 'active' : ''}" on:click={() => activeQRType = 'sms'}>SMS</div>
        <!--<div class="sub-tab {activeQRType === 'otp' ? 'active' : ''}" on:click={() => activeQRType = 'otp'}>OTP</div>-->
        <div class="sub-tab {activeQRType === 'mms' ? 'active' : ''}" on:click={() => activeQRType = 'mms'}>MMS</div>
        <div class="sub-tab {activeQRType === 'bitcoin' ? 'active' : ''}" on:click={() => activeQRType = 'bitcoin'}>Bitcoin</div>
        <div class="sub-tab {activeQRType === 'bookmark' ? 'active' : ''}" on:click={() => activeQRType = 'bookmark'}>Bookmark</div>
        <div class="sub-tab {activeQRType === 'app' ? 'active' : ''}" on:click={() => activeQRType = 'app'}>App</div>
        <div class="sub-tab {activeQRType === 'event' ? 'active' : ''}" on:click={() => activeQRType = 'event'}>Event</div>
      </div>
    {/if}
  
    <!-- Conditionally render input fields based on active tab and QR code type -->
    <div class="form-fields">
      {#if activeTab === 'qr'}
        
        {#if activeQRType === 'text'}
          <input type="text" bind:value={text} placeholder="Enter text" />
        {/if}
  
        {#if activeQRType === 'url'}
          <input type="text" bind:value={text} placeholder="Enter URL" />
        {/if}
  
        {#if activeQRType === 'bitcoin'}
          <input type="text" bind:value={text} placeholder="Enter Bitcoin Address" />
        {/if}
  
        {#if activeQRType === 'bookmark'}
          <input type="text" bind:value={text} placeholder="Enter Bookmark URL" />
        {/if}
  
        {#if activeQRType === 'app'}
          <input type="text" bind:value={text} placeholder="Enter App Package Name" />
        {/if}
  
        {#if activeQRType === 'event'}
          <input type="text" bind:value={text} placeholder="Enter Event Name" />
        {/if}
  
        {#if activeQRType === 'wifi'}
          <input type="text" bind:value={ssid} placeholder="Enter SSID" />
          <input type="text" bind:value={password} placeholder="Enter Password" />
          <select bind:value={encryption}>
            <option value="WPA">WPA</option>
            <option value="WEP">WEP</option>
            <option value="nopass">No Encryption</option>
          </select>
        {/if}
  
        {#if activeQRType === 'location'}
          <input type="text" bind:value={lat} placeholder="Enter Latitude" />
          <input type="text" bind:value={long} placeholder="Enter Longitude" />
        {/if}
  
        {#if activeQRType === 'contact'}
          <input type="text" bind:value={name} placeholder="Enter Name" />
          <input type="text" bind:value={phone} placeholder="Enter Phone" />
          <input type="text" bind:value={email} placeholder="Enter Email" />
        {/if}
  
        {#if activeQRType === 'phone' || activeQRType === 'sms' || activeQRType === 'mms'}
          <input type="text" bind:value={phone} placeholder="Enter Phone Number" />
          {#if activeQRType === 'sms' || activeQRType === 'mms'}
            <input type="text" bind:value={text} placeholder="Enter Message" />
          {/if}
        {/if}
  
        {#if activeQRType === 'email'}
          <input type="text" bind:value={email} placeholder="Enter Email Address" />
        {/if}
      {/if}
  
      {#if activeTab === 'datamatrix' || activeTab === '2dbarcode'}
        <input type="text" bind:value={text} placeholder="Enter data" />
      {/if}
    </div>
  
    <button on:click={generateCode}>Generate</button>
  
    {#if imageDataUrl}
      <img src={imageDataUrl} alt="Generated code" />
      <button on:click={downloadPng}>Download PNG</button>
    {/if}
  </main>
  