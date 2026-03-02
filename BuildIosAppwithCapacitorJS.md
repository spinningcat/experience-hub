<h1 style="border-bottom:1px solid #e1e4e8;padding-bottom:8px;">
Building an iOS App with Capacitor
</h1>

<p><strong>Last updated:</strong> 2026-03-02</p>

<p>
This guide explains how to convert a Capacitor web app into a native iOS application, 
including setup steps, requirements, common issues, and fixes.
</p>

<hr>

<h2 style="border-bottom:1px solid #e1e4e8;padding-bottom:6px;">System Requirements</h2>

<ul>
<li>Mac running latest stable macOS</li>
<li>Xcode (latest stable version)</li>
<li>Node.js (LTS recommended)</li>
<li>CocoaPods installed</li>
<li>Apple Developer account (required for real device & App Store)</li>
</ul>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
# Install CocoaPods
sudo gem install cocoapods

# Check versions
node -v
npm -v
pod --version
</pre>

<hr>

<h2 style="border-bottom:1px solid #e1e4e8;padding-bottom:6px;">Step-by-Step iOS Setup</h2>

<h3>1️⃣ Install iOS Platform</h3>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
npm install @capacitor/ios
npx cap add ios
</pre>

<p>This creates the <code>ios/</code> directory with the native Xcode project.</p>

<h3>2️⃣ Build Web Assets</h3>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
npm run build
</pre>

<p>
Ensure <code>capacitor.config.json</code> has correct:
<code>"webDir": "dist"</code> (or your build folder).
</p>

<h3>3️⃣ Sync to Native Project</h3>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
npx cap sync ios
</pre>

<p>Always run this after adding plugins or modifying config.</p>

<h3>4️⃣ Open in Xcode</h3>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
npx cap open ios
</pre>

<h3>5️⃣ Run on Simulator</h3>

<ul>
<li>Select a simulator device</li>
<li>Click ▶ Run</li>
</ul>

<h3>6️⃣ Run on Physical Device</h3>

<ul>
<li>Connect device via USB</li>
<li>Select device in Xcode</li>
<li>Go to <strong>Signing & Capabilities</strong></li>
<li>Enable "Automatically manage signing"</li>
<li>Select your development team</li>
<li>Trust developer profile in device settings</li>
</ul>

<hr>

<h2 style="border-bottom:1px solid #e1e4e8;padding-bottom:6px;">Common Problems & Fixes</h2>

<h3>❌ No valid code signing certificates found</h3>

<p><strong>Cause:</strong> No development team selected.</p>

<p><strong>Fix:</strong></p>
<ul>
<li>Enable Automatically Manage Signing</li>
<li>Add Apple ID in Xcode → Settings → Accounts</li>
</ul>

<hr>

<h3>❌ Sandbox: rsync.samba deny file-write-create</h3>

<p><strong>Cause:</strong> Derived data or permission issue.</p>

<p><strong>Fix:</strong></p>
<ul>
<li>Product → Clean Build Folder</li>
<li>Delete Derived Data</li>
<li>Restart Xcode</li>
</ul>

<hr>

<h3>❌ Command PhaseScriptExecution failed</h3>

<p><strong>Cause:</strong> CocoaPods not installed correctly.</p>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
cd ios/App
pod install --repo-update
</pre>

<p>If still failing:</p>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
rm -rf Pods Podfile.lock
pod install
</pre>

<hr>

<h3>❌ No such module 'Capacitor'</h3>

<p><strong>Cause:</strong> Native dependencies not synced.</p>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
npx cap sync ios
</pre>

<p>Or reset platform:</p>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
npx cap remove ios
npx cap add ios
npx cap sync ios
</pre>

<hr>

<h3>❌ App installs but crashes immediately</h3>

<p><strong>Cause:</strong> Framework not embedded properly.</p>

<p><strong>Fix:</strong></p>
<ul>
<li>Check Frameworks → Set to Embed & Sign</li>
<li>Run npx cap sync ios</li>
</ul>

<hr>

<h3>❌ dyld: Library not loaded</h3>

<p><strong>Fix:</strong> Reinstall pods and resync platform.</p>

<hr>

<h3>❌ Integrity could not be verified</h3>

<p><strong>Cause:</strong> Signing issue or corrupted install.</p>

<p><strong>Fix:</strong></p>
<ul>
<li>Delete app from device</li>
<li>Clean build folder</li>
<li>Rebuild and reinstall</li>
</ul>

<hr>

<h2 style="border-bottom:1px solid #e1e4e8;padding-bottom:6px;">Push Notifications Issue</h2>

<p>Error: <code>Provisioning profile doesn't include the aps-environment entitlement</code></p>

<p><strong>Fix:</strong></p>
<ul>
<li>Add Push Notifications capability</li>
<li>Enable Background Modes → Remote notifications</li>
</ul>

<hr>

<h2 style="border-bottom:1px solid #e1e4e8;padding-bottom:6px;">Building for App Store</h2>

<ol>
<li>Update Bundle Identifier & Version</li>
<li>Set Distribution signing</li>
<li>Product → Archive</li>
<li>Upload via Organizer</li>
</ol>

<hr>

<h2 style="border-bottom:1px solid #e1e4e8;padding-bottom:6px;">Handy Commands</h2>

<pre style="background:#f6f8fa;padding:12px;border-radius:6px;overflow:auto;">
# Add iOS
npx cap add ios

# Sync
npx cap sync ios

# Open
npx cap open ios

# Full Reset
rm -rf ios
npx cap add ios
npx cap sync ios
</pre>

<hr>

<p style="background:#f6f8fa;padding:12px;border-left:4px solid #2da44e;border-radius:6px;">
<strong>Best Practice:</strong> Always run <code>npx cap sync ios</code> after:
<br>• Installing plugins
<br>• Editing capacitor.config.json
<br>• Changing app permissions
</p>

<p>
<strong>You're ready to build, debug, and ship iOS apps with Capacitor.</strong> 🚀
</p>