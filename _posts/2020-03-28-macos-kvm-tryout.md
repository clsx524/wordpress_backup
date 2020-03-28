---
ID: 36
post_title: MacOS KVM Tryout
author: clsx524
post_excerpt: ""
layout: post
permalink: >
  https://wordpress.clsx524.page/2020/03/28/macos-kvm-tryout/
published: true
post_date: 2020-03-28 00:08:13
---
<!-- wp:paragraph {"align":null} -->
<p>This article is based on following tutorials. I want to document all changes I made as well as all hardwares I have purchased. Hope you can find them useful.</p>
<!-- /wp:paragraph -->

<!-- wp:list -->
<ul><li><a href="https://passthroughpo.st/new-and-improved-mac-os-tutorial-part-1-the-basics/">Tutorial 1</a>: New and Improved Mac OS Tutorial, Part 1 (The Basics)</li><li><a href="https://passthroughpo.st/mac-os-vm-guide-part-2-gpu-passthrough-and-tweaks/">Tutorial 2</a>: Mac OS VM Guide Part 2 (GPU Passthrough and Tweaks)</li></ul>
<!-- /wp:list -->

<!-- wp:heading {"level":3} -->
<h3>Devices</h3>
<!-- /wp:heading -->

<!-- wp:list -->
<ul><li>Intel CPU</li><li><a href="https://www.amazon.com/Sapphire-11265-05-20G-Backplate-Graphics-Graphic/dp/B06ZZ6FMF8/">Sapphire Radeon RX 580</a> (11265-05-20G). This card supports macOS out of box and I passed through this device int VM.</li><li><a href="https://www.amazon.com/gp/product/B07T9JD93Y">PCI Card</a> for WIFI and bluetooth. This card supports macOS out of box and I passed through this device int VM.</li><li><a href="https://www.amazon.com/gp/product/B00FPIMICA">PCI Card</a> for USB 3.0. This card supports macOS out of box and I passed through this PCI device completely into VM. There are two internal USB ports which can be used for connecting the data cable from the PCI card for bluetooth above. I also plugged a USB dongle for wireless audio card into another internal USB port. Both are hidden well inside of the case.</li><li>9PIN USB male header to USB 2.0 port. Used to connect the data port on bluetooth PCI card into the PCI card of USB 3.0. </li></ul>
<!-- /wp:list -->

<!-- wp:heading {"level":3} -->
<h3>Steps </h3>
<!-- /wp:heading -->

<!-- wp:list -->
<ul><li>Follow the steps in tutorial 1 and start macOS in virt-manager</li></ul>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>sudo apt install virt-manager
systemctl enable libvirtd.service virtlogd.service
systemctl start libvirtd.service and virtlogd.service</code></pre>
<!-- /wp:code -->

<!-- wp:list -->
<ul><li>Blacklist PCI devices that you want to use in VM. Add/update following in /etc/default/grub</li></ul>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>GRUB_CMDLINE_LINUX="intel_iommu=on iommu=pt module_blacklist=amdgpu,radeon vfio-pci.ids=&lt;id1>,&lt;id2>"</code></pre>
<!-- /wp:code -->

<!-- wp:list -->
<ul><li>run <code>update-grub</code> and reboot</li><li>add necessary PCI devices into virt-manager</li><li>start virt-manager and GPU should output the graphics with correct resolutions (up to your monitor)</li></ul>
<!-- /wp:list -->

<!-- wp:heading {"level":4} -->
<h4>Fix <strong>iMessage/AirDrop/Apple Services</strong></h4>
<!-- /wp:heading -->

<!-- wp:paragraph -->
<p>Ethernet should be en0 and marked as <code>builtin</code>. Follow the guidelines <a href="https://www.insanelymac.com/forum/topic/114349-how-to-ethernet-efi-strings-device-properties/">here</a> to get the hex string of your ethernet card and add it into <code>/Library/Preferences/SystemConfiguration/com.apple.Boot.plist</code>.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Don't forget to delete <code>/Library/Preferences/SystemConfiguration/NetworkInterfaces.plist </code> and reboot.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>You can download gfxutil from <a href="https://github.com/acidanthera/gfxutil/releases">here</a>.</p>
<!-- /wp:paragraph -->

<!-- wp:paragraph -->
<p>Next requirement is to set correct smbios. You can follow this <a href="https://www.youtube.com/watch?v=3xn9CpRjkf4">video</a> tutorial.</p>
<!-- /wp:paragraph -->

<!-- wp:heading {"level":4} -->
<h4><strong>Multiple PCI devices in the same IOMMU group</strong></h4>
<!-- /wp:heading -->

<!-- wp:list -->
<ul><li>run this <a href="https://gist.github.com/mdPlusPlus/031ec2dac2295c9aaf1fc0b0e808e21a">gist</a> to install patched kernel</li><li>add following into /etc/default/grub and run <code>update-grub</code> and reboot</li></ul>
<!-- /wp:list -->

<!-- wp:code -->
<pre class="wp-block-code"><code>GRUB_CMDLINE_LINUX="pcie_acs_override=downstream,multifunction"</code></pre>
<!-- /wp:code -->

<!-- wp:heading {"level":4} -->
<h4>Virtual Network stopped working</h4>
<!-- /wp:heading -->

<!-- wp:code -->
<pre class="wp-block-code"><code>sudo virsh destroy default
sudo virsh net-start default</code></pre>
<!-- /wp:code -->