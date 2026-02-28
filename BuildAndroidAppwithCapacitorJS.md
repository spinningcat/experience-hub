<h1>Capacitor Android: Troubleshooting & Setup</h1>

<p><strong>Last updated:</strong> 2026-02-28</p>

<p>
This guide documents the steps to get a Capacitor app running on an Android emulator,
covering environment setup, Java version mismatches, Gradle configuration,
CORS handling, and debugging.
</p>

<hr>

<h2>Common Issues & Fixes</h2>

<h3>1. Android SDK not found (<code>ERR_SDK_NOT_FOUND</code>)</h3>

<p><strong>Cause:</strong> <code>ANDROID_HOME</code> or <code>ANDROID_SDK_ROOT</code> not set.</p>

<p><strong>Fix:</strong> Add to <code>~/.bashrc</code>:</p>

<pre><code>export ANDROID_HOME=/opt/android-sdk
export PATH=$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$ANDROID_HOME/cmdline-tools/latest/bin
</code></pre>

<p>Then run:</p>

<pre><code>source ~/.bashrc
</code></pre>

<hr>

<h3>2. Java compiler missing (JRE instead of JDK)</h3>

<p><strong>Cause:</strong> <code>java</code> works but <code>javac</code> fails.</p>

<p><strong>Fix:</strong></p>

<pre><code>sudo apt update
sudo apt install openjdk-21-jdk
javac -version
</code></pre>

<hr>

<h3>3. Gradle uses wrong Java home</h3>

<p><strong>Cause:</strong> <code>org.gradle.java.home</code> points to wrong JDK.</p>

<p>Edit <code>android/gradle.properties</code>:</p>

<pre><code>org.gradle.java.home=/usr/lib/jvm/java-21-openjdk-amd64
</code></pre>

<p>Stop daemon:</p>

<pre><code>cd android
./gradlew --stop
</code></pre>

<hr>

<h3>4. Java version mismatch</h3>

<p>Example errors:</p>

<pre><code>invalid source release: 21
invalid target release: 21
</code></pre>

<p><strong>Fix options:</strong></p>

<ul>
<li>Install required JDK (e.g., Java 21)</li>
<li>Downgrade <code>sourceCompatibility</code> and <code>targetCompatibility</code></li>
</ul>

<p>Example:</p>

<pre><code>compileOptions {
    sourceCompatibility JavaVersion.VERSION_17
    targetCompatibility JavaVersion.VERSION_17
}
</code></pre>

<hr>

<h3>5. App signature mismatch (<code>IncompatibleUpdateFailure</code>)</h3>

<p><strong>Cause:</strong> Previously installed with different key.</p>

<pre><code>adb uninstall com.example.app
</code></pre>

<hr>

<h3>6. Emulator system image missing</h3>

<pre><code>sdkmanager --licenses
sdkmanager "system-images;android-34;google_apis;x86_64"
sdkmanager "platforms;android-34"

avdmanager create avd -n Pixel6_API34 -k "system-images;android-34;google_apis;x86_64" -d pixel_6
emulator -avd Pixel6_API34
</code></pre>

<hr>

<h3>7. CORS errors in WebView</h3>

<p><strong>Fix:</strong> Use Capacitor HTTP plugin.</p>

<pre><code>npm install @capacitor/http
npx cap sync
</code></pre>

<p>Usage:</p>

<pre><code>if (window.Capacitor) {
  const response = await Capacitor.Plugins.CapacitorHttp.post({
    url: 'https://your-api.com/endpoint',
    headers: { 'Content-Type': 'application/json' },
    data: payload
  });
}
</code></pre>

<p><strong>Note:</strong> Do not <code>JSON.stringify</code> the <code>data</code> field.</p>

<hr>

<h2>Debugging with Chrome DevTools</h2>

<ol>
<li>Open Chrome</li>
<li>Go to <code>chrome://inspect</code></li>
<li>Ensure device appears (<code>adb devices</code>)</li>
<li>Click <strong>Inspect</strong></li>
<li>Use Console and Network tabs</li>
</ol>

<hr>

<h2>Quick Command Reference</h2>

<pre><code># Check Java
java -version
javac -version

# List JDKs
update-java-alternatives --list

# Gradle daemon JVM
cd android
./gradlew -version | grep "Daemon JVM"

# Install APK
adb install android/app/build/outputs/apk/debug/app-debug.apk

# View logs
adb logcat | grep -E "AndroidRuntime|com.example.app|ERROR"
</code></pre>

<hr>

<h2>Summary</h2>

<ul>
<li>Set <code>ANDROID_HOME</code></li>
<li>Use full JDK</li>
<li>Match Java versions</li>
<li>Override Gradle Java if needed</li>
<li>Use <code>@capacitor/http</code> for CORS</li>
<li>Debug with <code>chrome://inspect</code></li>
</ul>

<p><strong>Final result:</strong> App builds, runs, API works, debugging works.</p>