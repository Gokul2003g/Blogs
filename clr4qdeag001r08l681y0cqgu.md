---
title: "Journey to Debian: Embracing Stability and Efficiency"
seoTitle: "Mastering Linux: A Journey through Distributions, Tiling Window Manage"
seoDescription: "Embark on a comprehensive Linux journey exploring distributions, Tiling Window Managers, and KDE customization. Discover tips, challenges, and the quest for"
datePublished: Mon Jan 08 2024 09:38:48 GMT+0000 (Coordinated Universal Time)
cuid: clr4qdeag001r08l681y0cqgu
slug: journey-to-debian-embracing-stability-and-efficiency
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/NLSXFjl_nhc/upload/a8151f84357bd9a4036008c2b4f93ec5.jpeg
tags: kde, linux, neovim, debian, lazyvim

---

### **A Journey Through Linux Distributions**

My journey into the world of Linux started with Ubuntu, providing a user-friendly introduction to the realm of open-source operating systems. I used it for about a month before I switched to Fedora which was my primary system for about a year, till I saw Arch install videos on Youtube that were almost 40 mins long, (this is before the ArchInstall script).

So I took upon the challenge to install Arch on my own using the documentation only, but ended up not having a working system for a day, but I eventually got it. However, once the installation was complete, there wasn't much excitement as it turned out to be just another Linux system running KDE Plasma.

Thanks to [DistroTube](https://www.youtube.com/@distrotube) , I experimented with Tiling Window Managers (TWM). After some exploration, I began with i3wm, which is considered suitable for beginners. The joy of tweaking and customizing your system regularly is what Tiling Window Managers offer. However, I eventually realized it was consuming a lot of my productive time. Thus, I decided to transition to a conventional Desktop Environment, but I couldn't let go of the efficiency and enjoyment offered by a Tiling Window Manager.

I considered moving to Pop OS for its auto-tiling feature, but it didn't match the experience of a true TWM. That's when I learned about KDE Bismuth, which supported Tiling in KDE. I attempted to try it out with Kubuntu, but unfortunately, it wasn't available at that time. Consequently, I switched to Manjaro with KDE and Bismuth, and that became my system for about another year. However, I encountered dependency issues while installing software using Pacman and Yay package managers, which led to what felt like a 'dependency hell.' This led me to move to my current system.

### The Perfect Balance

![Perfectly Balanced Thanos GIF - Perfectly Balanced Thanos Infinity War GIFs](https://media1.tenor.com/m/Hqyg8s_gh5QAAAAC/perfectly-balanced-thanos.gif align="center")

Here is my current system—a perfect blend of Debian's rock-solid stability, KDE's customizability, and Bismuth providing the ideal tiling experience.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704702693518/db4828db-324e-4a8e-ab12-789f96ee9414.png align="center")

To avoid bloat from KDE, I installed Debian without a desktop environment and then selectively installed only the 'kde-plasma-desktop' package using

```bash
sudo apt install kde-plasma-desktop
```

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704702955248/6758852b-b828-4d61-b337-459821347c48.png align="center")

* Install nala for better output instead of apt, though it is a personal choice
    
    ```bash
    sudo apt install nala
    ```
    
* Install the extension [Virtual-Desktop-Bar](https://github.com/wsdfhjxc/virtual-desktop-bar) and add it in the menu bar.
    
* Install Fluent Theme as Global theme
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704703501931/7321c9ec-0b52-4a87-b328-55ffae02e24b.png align="center")

* Install Kvantum Manager for consistent theme across different apps
    
    ```bash
    sudo nala install qt5-style-kvantum
    ```
    
* Install Fluent Dark theme in Kvantum Manager and choose the Fluent Dark theme. Also select kvantum-dark under Application style. You can download the theme [here](https://store.kde.org/p/1280231)
    
* My terminal of choice is kitty because it allows for ligatures. I use JetBrainsNerd Mono Font for icons and ligature support. You can download the font [here](https://www.nerdfonts.com/font-downloads).
    
* Wallpapers make a big part of a eligent setup. I got them from this [repo](https://github.com/JustAnotherStrange/wallpapers).
    
* Get my shortcut scheme [here](https://github.com/Gokul2003g/config).
    

Feel free to modify everything to suit your needs.

### LazyVim

Another important part of my setup is my editor of choice LazyVim.

Talking about LazyVim is a whole another topic in itself. You can find more about it in this video where [Harkirat](https://www.youtube.com/@harkirat1) talks about switching from VS Code to LazyVim.

%[https://youtu.be/F3iv2VPfMIc?si=eQujJ2NdCV_Sed6U] 

Or this video by [ElijahManor](https://www.youtube.com/@ElijahManor)

%[https://youtu.be/N93cTbtLCIM?si=G9oqjJfmCKtOp97R] 

Get LazyVim [here](https://www.lazyvim.org/).

But getting these Neovim distributions to work in debian is a hassle because they need latest neovim version. So install the latest using the nvim appimage:

(It might not work if your Linux distribution is more than 4 years old)

```bash
curl -LO https://github.com/neovim/neovim/releases/latest/download/nvim.appimage
chmod u+x nvim.appimage
./nvim.appimage
```

If the `./nvim.appimage` command fails, try:

```bash
./nvim.appimage --appimage-extract
./squashfs-root/AppRun --version
```

To expose nvim globally:

```bash
# Optional: exposing nvim globally.
sudo mv squashfs-root /
sudo ln -s /squashfs-root/AppRun /usr/bin/nvim
nvim
```

### Conclusion

Reflecting on my Linux journey, from Ubuntu to exploring Tiling Window Managers and discovering KDE Bismuth, it's been a quest for the perfect balance between productivity and customization. Despite encountering challenges like dependency issues, tools like IDE LazyVim have streamlined my workflow. This ongoing adventure in crafting my desktop setup has taught me the value of adaptability and exploration. In the dynamic world of Linux, I'm continually refining my workspace for an optimal, user-friendly experience, seeking that perfect synergy between functionality and personalization.