<div style="background-color: #0d1117; color: #c9d1d9; font-family: 'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, monospace; padding: 25px; border-radius: 8px; border: 1px solid #30363d; line-height: 1.5; max-width: 850px; margin: 20px auto;">

<h1 style="color: #58a6ff; border-bottom: 1px solid #30363d; padding-bottom: 10px; margin-top: 0;">SSH Troubleshooting: User `berat`</h1>

<h2 style="color: #ff7b72; font-size: 1.25rem;">Probken</h2>
<ul style="list-style-type: square;">
 <li>Initially unable to SSH as root (disabled by default) or as a newly created user berat.</li>
 <li>ssh-copy-id failed with Permission denied (publickey) even after configuring password authentication.</li>
 <li>SFTP access also denied. </li>
</ul>

<h2 style="color: #ffa657; font-size: 1.25rem;">Root Cause</h2>
<ul style="list-style-type: square;">
<li>Cloud-init overrides: Default SSH configs in /etc/ssh/sshd_config.d/ (e.g., 50-cloud-init.conf) were forcing PasswordAuthentication no, overriding changes made in the main sshd_config.</li>
 <li> Missing SSH key: User berat had no public key in ~/.ssh/authorized_keys, and password authentication was not actually enabled despite configuration attempts.</li>
 <li>Missing SSH key: User berat had no public key in ~/.ssh/authorized_keys, and password authentication was not actually enabled despite configuration attempts.</li>
</ul>

<h2 style="color: #7ee787; font-size: 1.25rem;">Resolve</h2>
<ol style="padding-left: 20px;">
<li><strong>Checked Active SSH Configuration</strong>
 <ul>
    <li>Used sshd -T | grep passwordauthentication to see the runtime value.</li>
    <li>Found passwordauthentication no despite manual edits. </li>
 </ul>
</li>
 <li><strong>Identified Conflicting Config Files</strong>
  <ul>
    <li>Located override files:
        <ul style="list-style-type: circle;">
            <li>/etc/ssh/sshd_config.d/50-cloud-init.conf</li>
            <li>/etc/ssh/sshd_config.d/60-cloudimg-settings.conf</li>
        </ul>
    </li>
    <li>Created a higher-priority file 99-local.conf with PasswordAuthentication yes to ensure it loads last.</li>
 </ul>
</li>
 <li><strong>Verified Configuration</strong>
   <ul>
    <li>Ran sshd -t to test syntax, then restarted SSH: systemctl restart ssh</li>
    <li>Confirmed with sshd -T | grep passwordauthentication → now yes.</li>
 </ul>
</li>
 <li><strong>Attempted SSH Key Copy</strong>
    <ul>
    <li>Even with password auth enabled, ssh-copy-id -o PreferredAuthentications=password still failed with publickey error.</li>
    <li>Verbose SSH output (ssh -vvv) showed server still only offering publickey authentication.</li>
 </ul>
</li>
  <li><strong>Checked Local Account Health</strong>
    <ul>
    <li>Verified user berat could log in locally: su - berat → successful.</li>
    <li>Checked PAM configuration and account status: all normal.</li>
 </ul>
</li>
  <li><strong>Manual Key Installation (The Fix)</strong>
    <div style="background: #161b22; padding: 10px; border-radius: 4px; border-left: 4px solid #7ee787; margin: 10px 0;">
    Used local su access to add the public key directly:<br>
    <code>mkdir -p ~/.ssh</code><br>
    <code>echo "ssh-ed25519 AAAA..." >> ~/.ssh/authorized_keys</code><br>
    <code>chmod 700 ~/.ssh</code><br>
    <code>chmod 600 ~/.ssh/authorized_keys</code>
    </div>
    <ul><li>This bypassed the SSH authentication layer and allowed key-based login.</li></ul>
</li>
  <li><strong>Successful SSH Login</strong>
    <ul>
    <li>ssh berat@&lt;server-ip&gt; worked immediately.</li>
    <li>SFTP also functioned correctly.</li>
 </ul>
</li>
</ol>

<h2 style="color: #d2a8ff; font-size: 1.25rem; margin-top: 30px; border-top: 1px solid #30363d; padding-top: 20px;">Commands Summary</h2>

<pre style="background-color: #161b22; color: #79c0ff; padding: 15px; border-radius: 6px; border: 1px solid #30363d; overflow-x: auto;">
# Check runtime config
sshd -T | grep passwordauthentication

# Find override files
grep -r "PasswordAuthentication" /etc/ssh/

# Create persistent override
echo "PasswordAuthentication yes" > /etc/ssh/sshd_config.d/99-local.conf

# Restart SSH
systemctl restart ssh

# Add key manually (as user)
mkdir -p ~/.ssh && chmod 700 ~/.ssh

echo "your-public-key" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
</pre>

</div>