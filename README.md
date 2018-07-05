# Ulubis

[![Join the chat at https://gitter.im/ulubis/Lobby](https://badges.gitter.im/ulubis/Lobby.svg)](https://gitter.im/ulubis/Lobby?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

![Ulubis in action](https://github.com/malcolmstill/ulubis/raw/master/ulubis.gif)

Ulubis is a Wayland compositor written in Common Lisp. It is inspired by FVWM and StumpWM. The idea is that it is easy to hack on, customise, define your own interaction modes, etc. (see [alt-tab-mode.lisp](https://github.com/malcolmstill/ulubis/blob/master/alt-tab-mode.lisp) as an example of defining a custom mode)

(I currently call it a compositor intentionally...let's get a bit more window management functionality in before calling it a WM)

## Installation of ulubis

- Ensure you have SBCL or CCL and Quicklisp installed.
- Build `ulubis` / `ulubis-sdl`
```
git clone https://github.com/malcolmstill/ulubis.git
cd ulubis
sbcl --eval '(load "build-ulubis.lisp")'
```

or

```
sbcl --eval '(load "build-ulubis-sdl.lisp")'
```

If quicklisp complains about not finding the dependencies it's because I don't have it in the official distribution. To
get around that add clone the dependency (e.g. `cl-drm`) to the `local-projects` dir of quicklisp.

## Running ulubis

To run `ulubis` the user must be a member of the `input` and `video` groups. Navigate to a virtual terminal and run

```
> ulubis
```

For the SDL2 backend simply run `ulubis-sdl` when in X.

## Configuration

Ulubis looks for the file `~/.ulubis.lisp` and loads it if it exists.

An example configuration is as follows:

```
(in-package :ulubis)

(if (string-equal (symbol-name ulubis-backend:backend-name) "backend-drm-gbm")
    (progn
      (setf (screen-width *compositor*) 1440)
      (setf (screen-height *compositor*) 900))
    (progn
      (setf (screen-width *compositor*) 900)
      (setf (screen-height *compositor*) 600)))

(set-keymap *compositor* "evdev" "apple" "gb" "" "")

(defun startup ()
  (swank-loader:init)
  (swank:create-server :port 4005 :style :spawn :dont-close t)
  (swank:set-package "ULUBIS")

  ;; Add 4 views (virtual desktops) using the desktop-mode as default
  (loop :for i :from 0 :to 3 :do (push-view 'desktop-mode))
  (setf (current-view *compositor*) (first (views *compositor*))))

```

## Hacking on ulubis

Download `ulubis` and its dependencies to quicklisp's `local-projects/` dir and hack away, rebuilding the executables as per installation.

## Contributors

All glory to our lovely contributors, please join us:

- [naryl](https://github.com/naryl) very kindly added a nicer cursor using cairo
- [cbaggers](https://github.com/cbaggers) very kindly updated various bits and pieces to use the latest CEPL tech


## Status

Ulubis is known to work with sbcl and ccl. I have only tested it on two machines which Intel graphics chips, please get in touch if it does / doesn't work with Nvidia or AMD cards. It is very alpha.

## Roadmap

The vague roadmap for ulubis is as follows (not necessarily in order):
- [x] Add screenshotting
- [ ] Wallpapers
- [ ] Add (an at least rudimentary) menu system
- [ ] Server-side decorations
- [ ] Add screen locking
- [ ] Add video capture
- [ ] Support multiple monitors
- [ ] Support more (most?) Wayland clients
- [ ] XWayland support
- [ ] Potentially define custom Wayland protocols for ulubis (maybe you want to replace a built-in menu with your own menu written in QML)

## Dependencies

Ulubis depends on:
- libwayland
- [cl-wayland](https://github.com/malcolmstill/cl-wayland)
- libxkbcommon
- [cl-xkb](https://github.com/malcolmstill/cl-xkb)
- [cepl](https://github.com/cbaggers/cepl)
- [vydd's easing library](https://github.com/vydd/easing)
- [osicat](https://github.com/osicat/osicat)
- trivial-dump-core
- trivial-backtrace

Ulubis has two backends: [ulubis-sdl](https://github.com/malcolmstill/ulubis-sdl) (an SDL2 backend) and [ulubis-drm-gbm](https://github.com/malcolmstill/ulubis-drm-gbm) (a DRM/GBM backend). The DRM/GBM backend is intended to be *the* backend whilst the SDL2 is intended for testing on X.

The DRM/GBM backend depends on:
- libdrm 
- libgbm 
- libEGL
- [cl-drm](https://github.com/malcolmstill/cl-drm)
- [cl-gbm](https://github.com/malcolmstill/cl-gbm)
- [cl-egl](https://github.com/malcolmstill/cl-egl)
- [cepl.drm-gbm](https://github.com/malcolmstill/cepl.drm-gbm)
- [cl-libinput](https://github.com/malcolmstill/cl-libinput)

The dependencies for the SDL2 backend are:
- SDL2
- [cepl.sdl2](https://github.com/cbaggers/cepl.sdl2)


