# Third-party notices

aiVoiceTools is built with the open source software listed below. Each build carries the full license texts in `THIRD-PARTY-LICENSES.txt`:

- **Mac:** inside the app, at `aiVoiceTools.app/Contents/Resources/THIRD-PARTY-LICENSES.txt`
- **Windows:** next to `aivoicetools.exe`, in the zip and in the install folder
- **Linux:** `/usr/share/doc/<package>/THIRD-PARTY-LICENSES.txt`

On the Mac, `mlx-sys` also brings in mlx-c and what it builds with: MLX by Apple, fmt, gguflib, JSON for Modern C++ and metal-cpp.

The voice and transcription models are not part of the download. The app fetches them the first time they are needed, and their licenses are listed under Credits in the [README](README.md#credits).

This list is for aiVoiceTools 0.3.0 and has 445 entries.

| Component | Version | License | Used on |
|---|---|---|---|
| Breeze-TTS-2.cpp by HoppouAI |  | Apache-2.0 | Mac, Windows, Linux |
| CUDA runtime and cuBLAS (cublas64_12.dll, cublasLt64_12.dll) by NVIDIA Corporation |  | NVIDIA CUDA Toolkit End User License Agreement; these files are redistributable under its Attachment A. | Windows |
| ggml |  | MIT | Mac, Windows, Linux |
| SPIRV-Headers by The Khronos Group |  | MIT | Windows, Linux |
| Vulkan-Headers and Vulkan-Hpp by The Khronos Group |  | Apache-2.0 OR MIT | Windows, Linux |
| ab_glyph | 0.2.32 | Apache-2.0 | Mac, Windows, Linux |
| ab_glyph_rasterizer | 0.1.10 | Apache-2.0 | Mac, Windows, Linux |
| accesskit | 0.19.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| accesskit_atspi_common | 0.12.0 | MIT OR Apache-2.0 | Linux |
| accesskit_consumer | 0.28.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| accesskit_macos | 0.20.0 | MIT OR Apache-2.0 | Mac |
| accesskit_unix | 0.15.0 | MIT OR Apache-2.0 | Linux |
| accesskit_windows | 0.27.0 | MIT OR Apache-2.0 | Windows |
| accesskit_winit | 0.27.0 | Apache-2.0 | Mac, Windows, Linux |
| adler2 | 2.0.1 | 0BSD OR MIT OR Apache-2.0 | Mac, Windows, Linux |
| ahash | 0.8.12 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| aho-corasick | 1.1.5 | Unlicense OR MIT | Mac, Windows, Linux |
| alsa | 0.11.0 | Apache-2.0/MIT | Linux |
| alsa-sys | 0.4.0 | MIT | Linux |
| arboard | 3.7.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| arrayvec | 0.7.8 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| as-raw-xcb-connection | 1.0.1 | MIT OR Apache-2.0 | Linux |
| ascii | 1.1.0 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| async-broadcast | 0.7.2 | MIT OR Apache-2.0 | Linux |
| async-channel | 2.5.0 | Apache-2.0 OR MIT | Linux |
| async-executor | 1.14.0 | Apache-2.0 OR MIT | Linux |
| async-io | 2.6.0 | Apache-2.0 OR MIT | Linux |
| async-lock | 3.4.2 | Apache-2.0 OR MIT | Linux |
| async-process | 2.5.0 | Apache-2.0 OR MIT | Linux |
| async-recursion | 1.2.0 | MIT OR Apache-2.0 | Linux |
| async-signal | 0.2.14 | Apache-2.0 OR MIT | Linux |
| async-task | 4.7.1 | Apache-2.0 OR MIT | Linux |
| async-trait | 0.1.92 | MIT OR Apache-2.0 | Linux |
| atomic-waker | 1.1.2 | Apache-2.0 OR MIT | Linux |
| atspi | 0.25.0 | Apache-2.0 OR MIT | Linux |
| atspi-common | 0.9.0 | Apache-2.0 OR MIT | Linux |
| atspi-connection | 0.9.0 | Apache-2.0 OR MIT | Linux |
| atspi-proxies | 0.9.0 | Apache-2.0 OR MIT | Linux |
| autocfg | 1.5.1 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| base64 | 0.13.1 | MIT/Apache-2.0 | Mac, Windows, Linux |
| base64 | 0.22.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| bindgen | 0.72.1 | BSD-3-Clause | Mac |
| bit-set | 0.8.0 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| bit-vec | 0.8.0 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| bitflags | 1.3.2 | MIT/Apache-2.0 | Mac, Windows, Linux |
| bitflags | 2.13.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| block-buffer | 0.12.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| block2 | 0.5.1 | MIT | Mac |
| block2 | 0.6.2 | MIT | Mac |
| blocking | 1.7.0 | Apache-2.0 OR MIT | Linux |
| bytemuck | 1.25.2 | Zlib OR Apache-2.0 OR MIT | Mac, Windows, Linux |
| bytemuck_derive | 1.12.1 | Zlib OR Apache-2.0 OR MIT | Mac, Windows, Linux |
| byteorder | 1.5.0 | Unlicense OR MIT | Mac, Windows, Linux |
| byteorder-lite | 0.1.0 | Unlicense OR MIT | Mac, Windows, Linux |
| calloop | 0.13.0 | MIT | Linux |
| calloop | 0.14.5 | MIT | Linux |
| calloop-wayland-source | 0.3.0 | MIT | Linux |
| calloop-wayland-source | 0.4.1 | MIT | Linux |
| castaway | 0.2.4 | MIT | Mac, Windows, Linux |
| cc | 1.10.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| cexpr | 0.6.0 | Apache-2.0/MIT | Mac |
| cfg-if | 1.0.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| cfg_aliases | 0.2.2 | MIT | Mac, Windows, Linux |
| cgl | 0.3.2 | MIT / Apache-2.0 | Mac |
| chrono | 0.4.45 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| chunked_transfer | 1.5.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| clang-sys | 1.9.1 | Apache-2.0 | Mac |
| claxon | 0.4.3 | Apache-2.0 | Mac, Windows, Linux |
| clipboard-win | 5.4.3 | BSL-1.0 | Windows |
| cmake | 0.1.58 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| codespan-reporting | 0.12.0 | Apache-2.0 | Mac, Windows, Linux |
| compact_str | 0.9.1 | MIT | Mac, Windows, Linux |
| concurrent-queue | 2.5.0 | Apache-2.0 OR MIT | Linux |
| const-oid | 0.10.2 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| core-foundation | 0.9.4 | MIT OR Apache-2.0 | Mac |
| core-foundation-sys | 0.8.7 | MIT OR Apache-2.0 | Mac |
| core-graphics | 0.23.2 | MIT OR Apache-2.0 | Mac |
| core-graphics-types | 0.1.3 | MIT OR Apache-2.0 | Mac |
| core_detect | 1.0.0 | MIT/Apache-2.0 | Windows, Linux |
| coreaudio-rs | 0.14.2 | MIT/Apache-2.0 | Mac |
| cpal | 0.18.2 | Apache-2.0 | Mac, Windows, Linux |
| cpufeatures | 0.3.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| crc32fast | 1.5.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| crossbeam-deque | 0.8.8 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| crossbeam-epoch | 0.9.21 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| crossbeam-utils | 0.8.23 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| crypto-common | 0.2.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| cursor-icon | 1.2.0 | MIT OR Apache-2.0 OR Zlib | Mac, Windows, Linux |
| darling | 0.20.11 | MIT | Mac, Windows, Linux |
| darling | 0.21.3 | MIT | Mac |
| darling_core | 0.20.11 | MIT | Mac, Windows, Linux |
| darling_core | 0.21.3 | MIT | Mac |
| darling_macro | 0.20.11 | MIT | Mac, Windows, Linux |
| darling_macro | 0.21.3 | MIT | Mac |
| dary_heap | 0.3.9 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| dasp_sample | 0.11.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| derive_builder | 0.20.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| derive_builder_core | 0.20.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| derive_builder_macro | 0.20.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| digest | 0.11.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| directories | 6.0.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| dirs-sys | 0.5.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| dispatch | 0.2.0 | MIT | Mac |
| dispatch2 | 0.3.1 | Zlib OR Apache-2.0 OR MIT | Mac |
| displaydoc | 0.2.7 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| dlib | 0.5.3 | MIT | Linux |
| document-features | 0.2.12 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| downcast-rs | 1.2.1 | MIT/Apache-2.0 | Linux |
| dpi | 0.1.2 | Apache-2.0 AND MIT | Mac, Windows, Linux |
| dyn-clone | 1.0.20 | MIT OR Apache-2.0 | Mac |
| ecolor | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| eframe | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| egui | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| egui-wgpu | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| egui-winit | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| egui_glow | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| either | 1.19.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| emath | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| encoding_rs | 0.8.44 | (Apache-2.0 OR MIT) AND BSD-3-Clause | Mac, Windows, Linux |
| endi | 1.1.1 | MIT | Linux |
| enumflags2 | 0.7.12 | MIT OR Apache-2.0 | Linux |
| enumflags2_derive | 0.7.12 | MIT OR Apache-2.0 | Linux |
| epaint | 0.32.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| epaint_default_fonts | 0.32.3 | (MIT OR Apache-2.0) AND OFL-1.1 AND Ubuntu-font-1.0 | Mac, Windows, Linux |
| equivalent | 1.0.3 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| errno | 0.3.14 | MIT OR Apache-2.0 | Linux |
| error-code | 3.4.0 | BSL-1.0 | Windows |
| esaxx-rs | 0.1.10 | Apache-2.0 | Mac, Windows, Linux |
| event-listener | 5.4.2 | Apache-2.0 OR MIT | Linux |
| event-listener-strategy | 0.5.4 | Apache-2.0 OR MIT | Linux |
| fancy-regex | 0.14.0 | MIT | Mac, Windows, Linux |
| fastrand | 2.5.0 | Apache-2.0 OR MIT | Mac, Linux |
| fax | 0.2.7 | MIT | Mac, Windows, Linux |
| fdeflate | 0.3.7 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| find-msvc-tools | 0.1.15 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| flate2 | 1.1.10 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| fnv | 1.0.7 | Apache-2.0 / MIT | Mac, Windows, Linux |
| foldhash | 0.1.5 | Zlib | Mac, Windows, Linux |
| foreign-types | 0.5.0 | MIT/Apache-2.0 | Mac |
| foreign-types-macros | 0.2.4 | MIT/Apache-2.0 | Mac |
| foreign-types-shared | 0.3.1 | MIT/Apache-2.0 | Mac |
| form_urlencoded | 1.2.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| futures-core | 0.3.34 | MIT OR Apache-2.0 | Linux |
| futures-io | 0.3.34 | MIT OR Apache-2.0 | Linux |
| futures-lite | 2.6.1 | Apache-2.0 OR MIT | Linux |
| futures-macro | 0.3.34 | MIT OR Apache-2.0 | Linux |
| futures-task | 0.3.34 | MIT OR Apache-2.0 | Linux |
| futures-util | 0.3.34 | MIT OR Apache-2.0 | Linux |
| gethostname | 1.1.0 | Apache-2.0 | Linux |
| getrandom | 0.2.17 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| getrandom | 0.3.4 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| getrandom | 0.4.3 | MIT OR Apache-2.0 | Windows |
| gl_generator | 0.14.0 | Apache-2.0 | Windows, Linux |
| glob | 0.3.4 | MIT OR Apache-2.0 | Mac |
| glow | 0.16.0 | MIT OR Apache-2.0 OR Zlib | Mac, Windows, Linux |
| glutin | 0.32.3 | Apache-2.0 | Mac, Windows, Linux |
| glutin-winit | 0.5.0 | MIT | Mac, Windows, Linux |
| glutin_egl_sys | 0.7.1 | Apache-2.0 | Windows, Linux |
| glutin_glx_sys | 0.6.1 | Apache-2.0 | Linux |
| glutin_wgl_sys | 0.6.1 | Apache-2.0 | Windows |
| half | 2.7.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| hashbrown | 0.15.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| hashbrown | 0.17.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| heck | 0.5.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| hex | 0.4.3 | MIT OR Apache-2.0 | Linux |
| hexf-parse | 0.2.1 | CC0-1.0 | Mac, Windows, Linux |
| hound | 3.5.1 | Apache-2.0 | Mac, Windows, Linux |
| httpdate | 1.0.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| hybrid-array | 0.4.15 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| iana-time-zone | 0.1.65 | MIT OR Apache-2.0 | Mac, Linux |
| icu_collections | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| icu_locale_core | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| icu_normalizer | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| icu_normalizer_data | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| icu_properties | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| icu_properties_data | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| icu_provider | 2.4.0 | Unicode-3.0 | Mac, Windows, Linux |
| ident_case | 1.0.1 | MIT/Apache-2.0 | Mac, Windows, Linux |
| idna | 1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| idna_adapter | 1.2.2 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| image | 0.25.10 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| indexmap | 2.14.2 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| itertools | 0.13.0 | MIT OR Apache-2.0 | Mac |
| itertools | 0.14.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| itoa | 1.0.18 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| jobserver | 0.1.35 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| khronos_api | 3.1.0 | Apache-2.0 | Windows, Linux |
| lazy_static | 1.5.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| lewton | 0.10.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| libc | 0.2.191 | MIT OR Apache-2.0 | Mac, Linux |
| libloading | 0.8.9 | ISC | Mac, Windows, Linux |
| libm | 0.2.16 | MIT | Mac, Windows, Linux |
| linux-raw-sys | 0.12.1 | Apache-2.0 WITH LLVM-exception OR Apache-2.0 OR MIT | Linux |
| linux-raw-sys | 0.4.15 | Apache-2.0 WITH LLVM-exception OR Apache-2.0 OR MIT | Linux |
| litemap | 0.8.3 | Unicode-3.0 | Mac, Windows, Linux |
| litrs | 1.0.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| lock_api | 0.4.14 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| log | 0.4.34 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| mach-sys | 0.5.4 | GPL-3.0 OR Apache-2.0 | Mac |
| mach2 | 0.6.0 | BSD-2-Clause OR MIT OR Apache-2.0 | Mac |
| macro_rules_attribute | 0.2.3 | Apache-2.0 OR MIT OR Zlib | Mac, Windows, Linux |
| macro_rules_attribute-proc_macro | 0.2.3 | Apache-2.0 OR MIT OR Zlib | Mac, Windows, Linux |
| memchr | 2.8.3 | Unlicense OR MIT | Mac, Windows, Linux |
| memmap2 | 0.9.12 | MIT OR Apache-2.0 | Linux |
| memoffset | 0.9.1 | MIT | Mac, Windows, Linux |
| minimal-lexical | 0.2.1 | MIT/Apache-2.0 | Mac, Windows, Linux |
| miniz_oxide | 0.8.9 | MIT OR Zlib OR Apache-2.0 | Mac, Windows, Linux |
| miniz_oxide | 0.9.1 | MIT OR Zlib OR Apache-2.0 | Mac, Windows, Linux |
| mlx-internal-macros | 0.33.0 | MIT OR Apache-2.0 | Mac |
| mlx-macros | 0.33.0 | MIT OR Apache-2.0 | Mac |
| mlx-rs | 0.33.0 | MIT OR Apache-2.0 | Mac |
| mlx-sys | 0.7.0 | MIT OR Apache-2.0 | Mac |
| monostate | 0.1.18 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| monostate-impl | 0.1.18 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| moxcms | 0.8.1 | BSD-3-Clause OR Apache-2.0 | Mac, Windows, Linux |
| multiversion_no_op | 1.0.0 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| naga | 25.0.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| nohash-hasher | 0.2.0 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| nom | 7.1.3 | MIT | Mac, Windows, Linux |
| num-complex | 0.4.6 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| num-integer | 0.1.47 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| num-traits | 0.2.19 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| num_enum | 0.7.6 | BSD-3-Clause OR MIT OR Apache-2.0 | Mac |
| num_enum_derive | 0.7.6 | BSD-3-Clause OR MIT OR Apache-2.0 | Mac |
| objc-sys | 0.3.5 | MIT | Mac |
| objc2 | 0.5.3 | MIT | Mac |
| objc2 | 0.6.5 | MIT | Mac |
| objc2-app-kit | 0.2.2 | MIT | Mac |
| objc2-app-kit | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-audio-toolbox | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-core-audio | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-core-audio-types | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-core-data | 0.2.2 | MIT | Mac |
| objc2-core-foundation | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-core-graphics | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-core-image | 0.2.2 | MIT | Mac |
| objc2-encode | 4.1.0 | MIT | Mac |
| objc2-foundation | 0.2.2 | MIT | Mac |
| objc2-foundation | 0.3.2 | MIT | Mac |
| objc2-io-surface | 0.3.2 | Zlib OR Apache-2.0 OR MIT | Mac |
| objc2-metal | 0.2.2 | MIT | Mac |
| objc2-quartz-core | 0.2.2 | MIT | Mac |
| ogg | 0.8.0 | BSD-3-Clause | Mac, Windows, Linux |
| once_cell | 1.21.4 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| option-ext | 0.2.0 | MPL-2.0 | Mac, Windows, Linux |
| ordered-stream | 0.2.0 | MIT OR Apache-2.0 | Linux |
| owned_ttf_parser | 0.25.1 | Apache-2.0 | Mac, Windows, Linux |
| parking | 2.2.1 | Apache-2.0 OR MIT | Linux |
| parking_lot | 0.12.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| parking_lot_core | 0.9.12 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| paste | 1.0.15 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| pastey | 0.2.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| percent-encoding | 2.3.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| pin-project-lite | 0.2.17 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| piper | 0.2.5 | MIT OR Apache-2.0 | Linux |
| pkg-config | 0.3.34 | MIT OR Apache-2.0 | Linux |
| png | 0.18.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| polling | 3.11.0 | Apache-2.0 OR MIT | Linux |
| pollster | 0.4.0 | Apache-2.0/MIT | Linux |
| potential_utf | 0.1.6 | Unicode-3.0 | Mac, Windows, Linux |
| ppv-lite86 | 0.2.21 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| prettyplease | 0.2.37 | MIT OR Apache-2.0 | Mac |
| primal-check | 0.3.4 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| proc-macro-crate | 3.5.0 | MIT OR Apache-2.0 | Mac, Linux |
| proc-macro2 | 1.0.107 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| profiling | 1.0.18 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| pxfm | 0.1.30 | BSD-3-Clause OR Apache-2.0 | Mac, Windows, Linux |
| quick-error | 2.0.1 | MIT/Apache-2.0 | Mac, Windows, Linux |
| quick-xml | 0.41.0 | MIT | Linux |
| quote | 1.0.47 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rand | 0.9.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rand_chacha | 0.9.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rand_core | 0.9.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| raw-window-handle | 0.6.2 | MIT OR Apache-2.0 OR Zlib | Mac, Windows, Linux |
| rayon | 1.12.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rayon-cond | 0.4.0 | Apache-2.0/MIT | Mac, Windows, Linux |
| rayon-core | 1.13.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| regex | 1.13.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| regex-automata | 0.4.18 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| regex-syntax | 0.8.11 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| renderdoc-sys | 1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rfd | 0.17.2 | MIT | Mac, Windows, Linux |
| ring | 0.17.14 | Apache-2.0 AND ISC | Mac, Windows, Linux |
| rustc-hash | 1.1.0 | Apache-2.0/MIT | Mac, Windows, Linux |
| rustc-hash | 2.1.3 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| rustfft | 6.4.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rustix | 0.38.44 | Apache-2.0 WITH LLVM-exception OR Apache-2.0 OR MIT | Linux |
| rustix | 1.1.6 | Apache-2.0 WITH LLVM-exception OR Apache-2.0 OR MIT | Linux |
| rustls | 0.23.46 | Apache-2.0 OR ISC OR MIT | Mac, Windows, Linux |
| rustls-pki-types | 1.15.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| rustls-webpki | 0.103.16 | ISC | Mac, Windows, Linux |
| rustversion | 1.0.23 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| ryu | 1.0.23 | Apache-2.0 OR BSL-1.0 | Mac, Windows, Linux |
| scoped-tls | 1.0.1 | MIT/Apache-2.0 | Linux |
| scopeguard | 1.2.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| serde | 1.0.229 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| serde_core | 1.0.229 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| serde_derive | 1.0.229 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| serde_json | 1.0.154 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| serde_repr | 0.1.21 | MIT OR Apache-2.0 | Linux |
| serde_spanned | 1.1.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| sha2 | 0.11.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| shlex | 1.3.0 | MIT OR Apache-2.0 | Mac |
| shlex | 2.0.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| signal-hook-registry | 1.4.8 | MIT OR Apache-2.0 | Linux |
| simd-adler32 | 0.3.10 | MIT | Mac, Windows, Linux |
| simdutf8 | 0.1.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| slab | 0.4.12 | MIT | Linux |
| smallvec | 1.16.4 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| smithay-client-toolkit | 0.19.2 | MIT | Linux |
| smithay-client-toolkit | 0.20.0 | MIT | Linux |
| smithay-clipboard | 0.7.3 | MIT | Linux |
| smol_str | 0.2.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| spm_precompiled | 0.1.4 | Apache-2.0 | Mac, Windows, Linux |
| stable_deref_trait | 1.2.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| static_assertions | 1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| strength_reduce | 0.2.4 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| strsim | 0.11.1 | MIT | Mac, Windows, Linux |
| strum | 0.26.3 | MIT | Mac, Windows, Linux |
| strum | 0.28.0 | MIT | Mac |
| strum_macros | 0.26.4 | MIT | Mac, Windows, Linux |
| strum_macros | 0.28.0 | MIT | Mac |
| subtle | 2.6.1 | BSD-3-Clause | Mac, Windows, Linux |
| symphonia | 0.5.5 | MPL-2.0 | Mac, Windows, Linux |
| symphonia-bundle-mp3 | 0.5.5 | MPL-2.0 | Mac, Windows, Linux |
| symphonia-core | 0.5.5 | MPL-2.0 | Mac, Windows, Linux |
| symphonia-metadata | 0.5.5 | MPL-2.0 | Mac, Windows, Linux |
| syn | 2.0.119 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| syn | 3.0.9 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| synstructure | 0.14.0 | MIT | Mac, Windows, Linux |
| termcolor | 1.4.1 | Unlicense OR MIT | Mac, Windows, Linux |
| thiserror | 1.0.69 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| thiserror | 2.0.21 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| thiserror-impl | 1.0.69 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| thiserror-impl | 2.0.21 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| tiff | 0.11.3 | MIT | Mac, Windows, Linux |
| tiny_http | 0.12.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| tinystr | 0.8.5 | Unicode-3.0 | Mac, Windows, Linux |
| tinyvec | 1.13.3 | Zlib OR Apache-2.0 OR MIT | Mac, Windows, Linux |
| tokenizers | 0.22.2 | Apache-2.0 | Mac, Windows, Linux |
| toml | 1.1.8+spec-1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| toml_datetime | 1.1.2+spec-1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| toml_edit | 0.25.17+spec-1.1.0 | MIT OR Apache-2.0 | Mac, Linux |
| toml_parser | 1.1.5+spec-1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| toml_writer | 1.1.3+spec-1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| tracing | 0.1.44 | MIT | Mac, Windows, Linux |
| tracing-attributes | 0.1.31 | MIT | Mac, Windows, Linux |
| tracing-core | 0.1.36 | MIT | Mac, Windows, Linux |
| transpose | 0.2.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| ttf-parser | 0.25.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| type-map | 0.5.1 | MIT/Apache-2.0 | Mac, Windows, Linux |
| typenum | 1.20.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| unicode-ident | 1.0.27 | (MIT OR Apache-2.0) AND Unicode-3.0 | Mac, Windows, Linux |
| unicode-normalization-alignments | 0.1.12 | MIT/Apache-2.0 | Mac, Windows, Linux |
| unicode-segmentation | 1.13.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| unicode-width | 0.2.3 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| unicode_categories | 0.1.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| untrusted | 0.9.0 | ISC | Mac, Windows, Linux |
| ureq | 2.12.1 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| url | 2.5.8 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| utf8_iter | 1.0.4 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| uuid | 1.29.0 | Apache-2.0 OR MIT | Linux |
| version_check | 0.9.5 | MIT/Apache-2.0 | Mac, Windows, Linux |
| wayland-backend | 0.3.17 | MIT | Linux |
| wayland-client | 0.31.15 | MIT | Linux |
| wayland-csd-frame | 0.3.0 | MIT | Linux |
| wayland-cursor | 0.31.14 | MIT | Linux |
| wayland-protocols | 0.32.13 | MIT | Linux |
| wayland-protocols-experimental | 20250721.0.1 | MIT | Linux |
| wayland-protocols-misc | 0.3.12 | MIT | Linux |
| wayland-protocols-plasma | 0.3.12 | MIT | Linux |
| wayland-protocols-wlr | 0.3.12 | MIT | Linux |
| wayland-scanner | 0.31.11 | MIT | Linux |
| wayland-sys | 0.31.11 | MIT | Linux |
| web-time | 1.1.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| webbrowser | 1.2.5 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| webpki-roots | 0.26.11 | CDLA-Permissive-2.0 | Mac, Windows, Linux |
| webpki-roots | 1.0.11 | CDLA-Permissive-2.0 | Mac, Windows, Linux |
| weezl | 0.1.12 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| wgpu | 25.0.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| wgpu-core | 25.0.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| wgpu-core-deps-windows-linux-android | 25.0.0 | MIT OR Apache-2.0 | Windows, Linux |
| wgpu-hal | 25.0.2 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| wgpu-types | 25.0.0 | MIT OR Apache-2.0 | Mac, Windows, Linux |
| winapi | 0.3.9 | MIT/Apache-2.0 | Windows |
| winapi-util | 0.1.11 | Unlicense OR MIT | Windows |
| windows | 0.61.3 | MIT OR Apache-2.0 | Windows |
| windows | 0.62.2 | MIT OR Apache-2.0 | Windows |
| windows-collections | 0.2.0 | MIT OR Apache-2.0 | Windows |
| windows-collections | 0.3.2 | MIT OR Apache-2.0 | Windows |
| windows-core | 0.61.2 | MIT OR Apache-2.0 | Windows |
| windows-core | 0.62.2 | MIT OR Apache-2.0 | Windows |
| windows-future | 0.2.1 | MIT OR Apache-2.0 | Windows |
| windows-future | 0.3.2 | MIT OR Apache-2.0 | Windows |
| windows-implement | 0.60.2 | MIT OR Apache-2.0 | Windows |
| windows-interface | 0.59.3 | MIT OR Apache-2.0 | Windows |
| windows-link | 0.1.3 | MIT OR Apache-2.0 | Windows |
| windows-link | 0.2.1 | MIT OR Apache-2.0 | Windows |
| windows-numerics | 0.2.0 | MIT OR Apache-2.0 | Windows |
| windows-numerics | 0.3.1 | MIT OR Apache-2.0 | Windows |
| windows-result | 0.3.4 | MIT OR Apache-2.0 | Windows |
| windows-result | 0.4.1 | MIT OR Apache-2.0 | Windows |
| windows-strings | 0.4.2 | MIT OR Apache-2.0 | Windows |
| windows-strings | 0.5.1 | MIT OR Apache-2.0 | Windows |
| windows-sys | 0.52.0 | MIT OR Apache-2.0 | Windows |
| windows-sys | 0.59.0 | MIT OR Apache-2.0 | Windows |
| windows-sys | 0.61.2 | MIT OR Apache-2.0 | Windows |
| windows-targets | 0.52.6 | MIT OR Apache-2.0 | Windows |
| windows-threading | 0.1.0 | MIT OR Apache-2.0 | Windows |
| windows-threading | 0.2.1 | MIT OR Apache-2.0 | Windows |
| windows_x86_64_msvc | 0.52.6 | MIT OR Apache-2.0 | Windows |
| winit | 0.30.13 | Apache-2.0 | Mac, Windows, Linux |
| winnow | 1.0.4 | MIT | Mac, Windows, Linux |
| writeable | 0.6.4 | Unicode-3.0 | Mac, Windows, Linux |
| x11-dl | 2.21.0 | MIT | Linux |
| x11rb | 0.13.2 | MIT OR Apache-2.0 | Linux |
| x11rb-protocol | 0.13.2 | MIT OR Apache-2.0 | Linux |
| xcursor | 0.3.11 | MIT | Linux |
| xkbcommon-dl | 0.4.2 | MIT | Linux |
| xkeysym | 0.2.1 | MIT OR Apache-2.0 OR Zlib | Linux |
| xml-rs | 0.8.29 | MIT | Windows, Linux |
| yoke | 0.8.4 | Unicode-3.0 | Mac, Windows, Linux |
| yoke-derive | 0.8.4 | Unicode-3.0 | Mac, Windows, Linux |
| zbus | 5.19.0 | MIT | Linux |
| zbus-lockstep | 0.5.2 | MIT | Linux |
| zbus-lockstep-macros | 0.5.2 | MIT | Linux |
| zbus_macros | 5.19.0 | MIT | Linux |
| zbus_names | 4.3.5 | MIT | Linux |
| zbus_xml | 5.2.1 | MIT | Linux |
| zcheapstr | 1.1.0 | MIT | Linux |
| zerocopy | 0.8.63 | BSD-2-Clause OR Apache-2.0 OR MIT | Mac, Windows, Linux |
| zerocopy-derive | 0.8.63 | BSD-2-Clause OR Apache-2.0 OR MIT | Mac, Windows, Linux |
| zerofrom | 0.1.8 | Unicode-3.0 | Mac, Windows, Linux |
| zerofrom-derive | 0.1.8 | Unicode-3.0 | Mac, Windows, Linux |
| zeroize | 1.9.1 | Apache-2.0 OR MIT | Mac, Windows, Linux |
| zerotrie | 0.2.5 | Unicode-3.0 | Mac, Windows, Linux |
| zerovec | 0.11.9 | Unicode-3.0 | Mac, Windows, Linux |
| zerovec-derive | 0.11.7 | Unicode-3.0 | Mac, Windows, Linux |
| zlib-rs | 0.6.8 | Zlib | Mac, Windows, Linux |
| zmij | 1.0.23 | MIT | Mac, Windows, Linux |
| zune-core | 0.5.3 | MIT OR Apache-2.0 OR Zlib | Mac, Windows, Linux |
| zune-jpeg | 0.5.15 | MIT OR Apache-2.0 OR Zlib | Mac, Windows, Linux |
| zvariant | 5.15.1 | MIT | Linux |
| zvariant_derive | 5.15.1 | MIT | Linux |
| zvariant_utils | 4.2.0 | MIT | Linux |
