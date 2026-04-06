---
layout: project_page
permalink: /

title: "PTC-Depth: Pose-Refined Monocular Depth Estimation with Temporal Consistency"
venue: CVPR 2026
authors:
    "Leezy Han<sup>1</sup>, Seunggyu Kim<sup>1</sup>, Dongseok Shim<sup>2</sup>, Hyeonbeom Lee<sup>1*</sup>"
affiliations:
    "<sup>1</sup>Ajou University, <sup>2</sup>Sony Creative AI Lab"
paper: https://arxiv.org/abs/2604.01791
code: https://github.com/ajou-arrl/ptc-depth
---

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.css">

<div class="compare3">
  <div class="compare3-tldr">
    <strong style="color: #22ceb1;">PTC-Depth</strong> estimates temporally consistent <span style="color: #22ceb1;">metric depth</span> using only <strong>a single camera</strong> and <strong>just how far it moved</strong> — no LiDAR needed. It works across <strong>RGB, NIR, and thermal</strong> imagery without any fine-tuning.
  </div>
  <div class="swiper compare3-swiper">
    <div class="swiper-wrapper">
      <div class="swiper-slide">
        <div class="compare3-container" data-src="./static/video/wheel_merged.webm" data-panels="3">
          <div class="video-loading"><div class="video-loading-spinner"></div></div>
          <div class="compare3-scene-label">Wheel Dataset - Roadside</div>
          <video class="compare3-src" muted playsinline autoplay src="./static/video/wheel_merged.webm" style="display:none;"></video>
          <canvas class="compare3-canvas"></canvas>
          <div class="compare3-label compare3-label-0">LiDAR</div>
          <div class="compare3-label compare3-label-1">RGB</div>
          <div class="compare3-label compare3-label-2">Thermal</div>
          <div class="compare3-bar compare3-bar-left"></div>
          <div class="compare3-bar compare3-bar-right"></div>
        </div>
      </div>
      <div class="swiper-slide">
        <div class="compare3-container" data-src="./static/video/ms2_day_merged.webm" data-panels="3">
          <div class="video-loading"><div class="video-loading-spinner"></div></div>
          <div class="compare3-scene-label">MS2 Dataset - Daytime</div>
          <video class="compare3-src" muted playsinline preload="auto" src="./static/video/ms2_day_merged.webm" style="display:none;"></video>
          <canvas class="compare3-canvas"></canvas>
          <div class="compare3-label compare3-label-0">NIR</div>
          <div class="compare3-label compare3-label-1">RGB</div>
          <div class="compare3-label compare3-label-2">Thermal</div>
          <div class="compare3-bar compare3-bar-left"></div>
          <div class="compare3-bar compare3-bar-right"></div>
        </div>
        <div class="compare3-toggle-wrap">
          <span class="compare3-toggle-label">LiDAR Overlay</span>
          <label class="compare3-toggle">
            <input type="checkbox" class="compare3-toggle-input" data-normal="./static/video/ms2_day_merged.webm" data-lidar="./static/video/ms2_day_lidar_merged.webm">
            <span class="compare3-toggle-slider"></span>
          </label>
        </div>
      </div>
      <div class="swiper-slide">
        <div class="compare3-container" data-src="./static/video/ms2_night_merged.webm" data-panels="3">
          <div class="video-loading"><div class="video-loading-spinner"></div></div>
          <div class="compare3-scene-label">MS2 Dataset - Nighttime</div>
          <video class="compare3-src" muted playsinline preload="auto" src="./static/video/ms2_night_merged.webm" style="display:none;"></video>
          <canvas class="compare3-canvas"></canvas>
          <div class="compare3-label compare3-label-0">NIR</div>
          <div class="compare3-label compare3-label-1">RGB</div>
          <div class="compare3-label compare3-label-2">Thermal</div>
          <div class="compare3-bar compare3-bar-left"></div>
          <div class="compare3-bar compare3-bar-right"></div>
        </div>
        <div class="compare3-toggle-wrap">
          <span class="compare3-toggle-label">LiDAR Overlay</span>
          <label class="compare3-toggle">
            <input type="checkbox" class="compare3-toggle-input" data-normal="./static/video/ms2_night_merged.webm" data-lidar="./static/video/ms2_night_lidar_merged.webm">
            <span class="compare3-toggle-slider"></span>
          </label>
        </div>
      </div>
    </div>
    <div class="swiper-button-prev"></div>
    <div class="swiper-button-next"></div>
    <div class="swiper-pagination"></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/swiper@11/swiper-bundle.min.js"></script>
<script>
document.addEventListener('DOMContentLoaded', function() {
  // Hide loading spinners when videos are ready
  document.querySelectorAll('video').forEach(function(v) {
    function hideSpinner() {
      var parent = v.closest('.compare3-container') || v.closest('.swiper-slide');
      if (parent) {
        var loader = parent.querySelector('.video-loading');
        if (loader) loader.remove();
      }
    }
    if (v.readyState >= 3) hideSpinner();
    else v.addEventListener('canplay', hideSpinner, { once: true });
  });

  var swiper = new Swiper('.compare3-swiper', {
    slidesPerView: 1,
    spaceBetween: 0,
    loop: false,
    allowTouchMove: false,
    pagination: { el: '.swiper-pagination', clickable: true },
    on: {
      slideChangeTransitionEnd: function() {
        document.querySelectorAll('.compare3-swiper .swiper-slide').forEach(function(slide) {
          var isActive = slide.classList.contains('swiper-slide-active');
          var v = slide.querySelector('.compare3-src');
          if (v) {
            if (isActive) {
              v.play().catch(function(){});
              var ldr = slide.querySelector('.video-loading');
              if (ldr) {
                v.addEventListener('canplay', function onReady() {
                  v.removeEventListener('canplay', onReady);
                  ldr.remove();
                });
                if (v.readyState >= 3) ldr.remove();
              }
            } else {
              v.pause();
            }
          }
        });
      }
    }
  });

  // 첫 슬라이드 영상 강제 재생
  var firstVid = document.querySelector('.compare3-swiper .swiper-slide-active .compare3-src');
  if (firstVid) firstVid.play().catch(function(){});

  // 화살표 루프: 직접 제어 (Swiper navigation 비활성, race condition 방지)
  document.querySelector('.compare3-swiper .swiper-button-next').addEventListener('click', function() {
    if (swiper.isEnd) swiper.slideTo(0);
    else swiper.slideNext();
  });
  document.querySelector('.compare3-swiper .swiper-button-prev').addEventListener('click', function() {
    if (swiper.isBeginning) swiper.slideTo(swiper.slides.length - 1);
    else swiper.slidePrev();
  });

  // 영상 한 번 재생 완료 시 다음 슬라이드로 자동 전환
  function bindCompare3Ended() {
    var active = document.querySelector('.compare3-swiper .swiper-slide-active .compare3-src');
    if (active) {
      active.onended = function() {
        if (swiper.isEnd) swiper.slideTo(0);
        else swiper.slideNext();
      };
    }
  }
  bindCompare3Ended();
  swiper.on('slideChangeTransitionEnd', bindCompare3Ended);

  document.querySelectorAll('.compare3-container').forEach(function(container) {
    var video = container.querySelector('.compare3-src');
    var canvas = container.querySelector('.compare3-canvas');
    var ctx = canvas.getContext('2d');
    var barL = container.querySelector('.compare3-bar-left');
    var barR = container.querySelector('.compare3-bar-right');
    var labels = container.querySelectorAll('.compare3-label');

    var posL = 33.33, posR = 66.66;
    var dragging = null;
    var panelW = 0, panelH = 0;

    function clamp(val, min, max) { return Math.max(min, Math.min(max, val)); }

    function initCanvas() {
      if (video.videoWidth) {
        panelW = video.videoWidth / 3;
        panelH = video.videoHeight;
        canvas.width = panelW;
        canvas.height = panelH;
      }
    }
    video.addEventListener('loadedmetadata', initCanvas);
    if (video.readyState >= 1) initCanvas();

    function drawFrame() {
      if (!panelW) {
        requestAnimationFrame(drawFrame);
        return;
      }
      var cw = canvas.width;
      var ch = canvas.height;

      // 바 위치를 픽셀로 변환
      var splitL = (posL / 100) * cw;
      var splitR = (posR / 100) * cw;

      // NIR (왼쪽 패널에서 왼쪽 구간)
      if (splitL > 0) {
        ctx.drawImage(video, 0, 0, splitL, panelH, 0, 0, splitL, ch);
      }
      // RGB (가운데 패널에서 가운데 구간)
      if (splitR - splitL > 0) {
        ctx.drawImage(video, panelW + splitL, 0, splitR - splitL, panelH, splitL, 0, splitR - splitL, ch);
      }
      // Thermal (오른쪽 패널에서 오른쪽 구간)
      if (cw - splitR > 0) {
        ctx.drawImage(video, panelW * 2 + splitR, 0, cw - splitR, panelH, splitR, 0, cw - splitR, ch);
      }

      requestAnimationFrame(drawFrame);
    }
    requestAnimationFrame(drawFrame);

    function updateUI() {
      barL.style.left = posL + '%';
      barR.style.left = posR + '%';
      labels[0].style.left = (posL / 2) + '%';
      labels[1].style.left = ((posL + posR) / 2) + '%';
      labels[2].style.left = ((posR + 100) / 2) + '%';
    }

    function getPos(e) {
      var rect = container.getBoundingClientRect();
      var x = (e.touches ? e.touches[0].clientX : e.clientX) - rect.left;
      return (x / rect.width) * 100;
    }

    barL.addEventListener('mousedown', function(e) { e.preventDefault(); dragging = 'left'; });
    barL.addEventListener('touchstart', function(e) { e.preventDefault(); dragging = 'left'; });
    barR.addEventListener('mousedown', function(e) { e.preventDefault(); dragging = 'right'; });
    barR.addEventListener('touchstart', function(e) { e.preventDefault(); dragging = 'right'; });

    document.addEventListener('mousemove', function(e) {
      if (!dragging) return;
      var p = getPos(e);
      if (dragging === 'left') posL = clamp(p, 5, posR - 5);
      else posR = clamp(p, posL + 5, 95);
      updateUI();
    });
    document.addEventListener('touchmove', function(e) {
      if (!dragging) return;
      var p = getPos(e);
      if (dragging === 'left') posL = clamp(p, 5, posR - 5);
      else posR = clamp(p, posL + 5, 95);
      updateUI();
    });
    document.addEventListener('mouseup', function() { dragging = null; });
    document.addEventListener('touchend', function() { dragging = null; });

    swiper.on('slideChangeTransitionEnd', function() { updateUI(); });

    // 토글 로직: LiDAR overlay 전환
    var slide = container.closest('.swiper-slide');
    var toggle = slide ? slide.querySelector('.compare3-toggle-input') : null;
    if (toggle) {
      toggle.addEventListener('change', function() {
        var newSrc = toggle.checked ? toggle.dataset.lidar : toggle.dataset.normal;
        var currentTime = video.currentTime;
        var wasPlaying = !video.paused;
        // Show spinner during load
        var existing = container.querySelector('.video-loading');
        if (!existing) {
          var loader = document.createElement('div');
          loader.className = 'video-loading';
          loader.innerHTML = '<div class="video-loading-spinner"></div>';
          container.appendChild(loader);
        }
        video.src = newSrc;
        video.load();
        video.addEventListener('canplay', function onReady() {
          video.removeEventListener('canplay', onReady);
          var ldr = container.querySelector('.video-loading');
          if (ldr) ldr.remove();
        });
        video.addEventListener('loadedmetadata', function onLoad() {
          video.removeEventListener('loadedmetadata', onLoad);
          panelW = video.videoWidth / 3;
          panelH = video.videoHeight;
          canvas.width = panelW;
          canvas.height = panelH;
          video.currentTime = currentTime;
          if (wasPlaying) video.play().catch(function(){});
        });
      });
    }

    updateUI();
  });
});
</script>

<div class="has-text-centered">
<h2>Abstract</h2>
</div>
<div class="content has-text-justified">
Monocular depth estimation (MDE) has been widely adopted in the perception systems of autonomous vehicles and mobile robots. However, existing approaches often struggle to maintain temporal consistency in depth estimation across consecutive frames. This inconsistency not only causes jitter but can also lead to estimation failures when the depth range changes abruptly. To address these challenges, this paper proposes a consistency-aware monocular depth estimation framework that leverages wheel odometry from a mobile robot to achieve stable and coherent depth predictions over time. Specifically, we estimate camera pose and sparse depth from triangulation using optical flow between consecutive frames. The sparse depth estimates are used to update a recursive Bayesian estimate of the metric scale, which is then applied to rescale the relative depth predicted by a pre-trained depth estimation foundation model. The proposed method is evaluated on the KITTI, TartanAir, MS2, and our own dataset, demonstrating robust and accurate depth estimation performance.
</div>
<img src="/static/image/Fig_1.jpg" alt="Overview" style="width: 100%; border-radius: 6px; margin-top: 0.5rem;">
<p style="text-align: center; font-size: 0.8rem; color: #888; margin-top: 0.4rem;">
  PTC-Depth recovers metric-scale depth maps and accumulates temporally consistent 3D point clouds from monocular video using only wheel odometry.
</p>

---

<div class="has-text-centered">
<h2>How It Works</h2>
</div>
<img src="/static/image/Fig_2.jpg" alt="Pipeline" style="width: 100%; border-radius: 6px; margin-bottom: 0.5rem;">
<div class="content has-text-justified" style="font-size: 0.95rem;">
Given consecutive monocular frames and a scalar displacement (from wheel odometry or GPS), PTC-Depth estimates camera pose via optical flow and recovers metric scale. Sparse depth is obtained through triangulation and fused with a foundation model's relative depth prediction using recursive Bayesian updates. A superpixel-based spatial refinement further stabilizes the scale across regions, producing temporally consistent metric depth without any training or fine-tuning. For full details, please refer to <a href="https://arxiv.org/abs/2604.01791">the paper</a>.
</div>

---

<div class="has-text-centered">
<h2>Quantitative Results</h2>
</div>
<div class="content has-text-justified" style="font-size: 0.95rem; margin-bottom: 1rem;">
  We evaluate PTC-Depth on 7 scenarios spanning RGB, NIR, and thermal imagery across 4 datasets. Our method requires only a monocular camera and a scalar displacement per frame — <strong>wheel odometry</strong> for our custom dataset, and <strong>GPS-derived displacement</strong> for KITTI, TartanAir, and MS2. No dataset-specific fine-tuning or additional depth sensors are used. PTC-Depth achieves the best or second-best accuracy on all out-of-distribution scenarios.
</div>

<div style="margin: 1rem 0; overflow-x: auto;">
<table class="ptc-table">
  <thead>
    <tr>
      <th rowspan="3">Method</th>
      <th colspan="8">RGB</th>
      <th colspan="4">Thermal / NIR</th>
    </tr>
    <tr>
      <th colspan="2">KITTI</th>
      <th colspan="2">TartanAir</th>
      <th colspan="2">Roadside</th>
      <th colspan="2">Forest</th>
      <th colspan="2">MS2</th>
      <th colspan="2">Roadside</th>
    </tr>
    <tr>
      <th>AbsRel↓</th><th>δ&lt;1.25↑</th>
      <th>AbsRel↓</th><th>δ&lt;1.25↑</th>
      <th>AbsRel↓</th><th>δ&lt;1.25↑</th>
      <th>AbsRel↓</th><th>δ&lt;1.25↑</th>
      <th>AbsRel↓</th><th>δ&lt;1.25↑</th>
      <th>AbsRel↓</th><th>δ&lt;1.25↑</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>UniDepth</td>
      <td><strong>0.047</strong></td><td><strong>0.977</strong></td>
      <td><u>0.503</u></td><td>0.176</td>
      <td><u>0.465</u></td><td><u>0.201</u></td>
      <td><u>0.444</u></td><td>0.088</td>
      <td><strong>0.205</strong></td><td><u>0.698</u></td>
      <td><strong>0.394</strong></td><td><u>0.245</u></td>
    </tr>
    <tr>
      <td>DA v2</td>
      <td>0.171</td><td>0.773</td>
      <td>0.513</td><td><u>0.372</u></td>
      <td>0.494</td><td>0.177</td>
      <td><strong>0.418</strong></td><td><u>0.336</u></td>
      <td>0.405</td><td>0.187</td>
      <td><u>0.527</u></td><td>0.193</td>
    </tr>
    <tr>
      <td>VDA</td>
      <td>0.356</td><td>0.321</td>
      <td>0.599</td><td>0.342</td>
      <td>2.198</td><td>0.010</td>
      <td>1.339</td><td>0.041</td>
      <td>0.590</td><td>0.078</td>
      <td>2.275</td><td>0.011</td>
    </tr>
    <tr class="ptc-ours">
      <td>PTC-Depth</td>
      <td><u>0.137</u></td><td><u>0.877</u></td>
      <td><strong>0.427</strong></td><td><strong>0.688</strong></td>
      <td><strong>0.309</strong></td><td><strong>0.725</strong></td>
      <td>0.480</td><td><strong>0.520</strong></td>
      <td><u>0.247</u></td><td><strong>0.700</strong></td>
      <td>0.570</td><td><strong>0.527</strong></td>
    </tr>
  </tbody>
</table>
</div>
<p style="text-align: center; font-size: 0.8rem; color: #888; margin-top: 0.3rem;">
  <strong style="color:#666;">Bold</strong>: best, <u>underline</u>: second best. All models use publicly available metric depth models.
</p>

---

<div class="has-text-centered">
<h2>Qualitative Comparison</h2>
</div>
<div class="content has-text-justified" style="font-size: 0.95rem; margin-bottom: 1rem;">
  We compare accumulated 3D point clouds from PTC-Depth against UniDepth and VDA. UniDepth produces accurate depth on KITTI (its training domain), but exhibits severe jitter and scale drift on unseen environments such as forests and thermal imagery. VDA maintains smoother temporal transitions, but its metric scale is often inaccurate, leading to distorted geometry. PTC-Depth produces geometrically consistent reconstructions across all scenarios.
</div>

<div class="depth-comparison-stack">
  <div class="swiper depth-comparison-swiper">
    <div class="swiper-wrapper">
      <div class="swiper-slide">
        <div class="depth-stack-scene">Wheel Dataset — Roadside (Thermal)</div>
        <div class="video-loading"><div class="video-loading-spinner"></div></div>
        <video class="depth-vid" muted playsinline autoplay preload="auto" src="./static/video/roadside_ours_uni.webm" data-uni="./static/video/roadside_ours_uni.webm" data-vda="./static/video/roadside_ours_vda.webm" style="width: 100%; border-radius: 8px; display: block;"></video>
      </div>
      <div class="swiper-slide">
        <div class="depth-stack-scene">Wheel Dataset — Forest</div>
        <div class="video-loading"><div class="video-loading-spinner"></div></div>
        <video class="depth-vid" muted playsinline preload="auto" src="./static/video/forest_ours_uni.webm" data-uni="./static/video/forest_ours_uni.webm" data-vda="./static/video/forest_ours_vda.webm" style="width: 100%; border-radius: 8px; display: block;"></video>
      </div>
      <div class="swiper-slide">
        <div class="depth-stack-scene">KITTI</div>
        <div class="video-loading"><div class="video-loading-spinner"></div></div>
        <video class="depth-vid" muted playsinline preload="auto" src="./static/video/kitti_ours_uni.webm" data-uni="./static/video/kitti_ours_uni.webm" data-vda="./static/video/kitti_ours_vda.webm" style="width: 100%; border-radius: 8px; display: block;"></video>
      </div>
    </div>
    <div class="swiper-button-prev"></div>
    <div class="swiper-button-next"></div>
    <div class="swiper-pagination"></div>
  </div>
  <div class="depth-stack-labels">
    <span class="ptc-brand" style="font-weight: 700;">PTC-Depth</span>
    <span class="depth-baseline-switcher">
      <span class="depth-arrow depth-arrow-left" onclick="switchBaseline(-1)">◀</span>
      <span class="depth-baseline-label">UniDepth</span>
      <span class="depth-arrow depth-arrow-right" onclick="switchBaseline(1)">▶</span>
    </span>
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  var depthSwiper = new Swiper('.depth-comparison-swiper', {
    slidesPerView: 1,
    spaceBetween: 0,
    loop: false,
    allowTouchMove: false,
    pagination: { el: '.depth-comparison-swiper .swiper-pagination', clickable: true },
    on: {
      slideChangeTransitionEnd: function() {
        document.querySelectorAll('.depth-comparison-swiper .swiper-slide').forEach(function(slide) {
          var isActive = slide.classList.contains('swiper-slide-active');
          var v = slide.querySelector('.depth-vid');
          if (v) {
            if (isActive) {
              if (v.readyState < 2) {
                v.load();
              }
              v.currentTime = 0;
              v.play().catch(function(){});
              var ldr = slide.querySelector('.video-loading');
              if (ldr) {
                v.addEventListener('canplay', function onReady() {
                  v.removeEventListener('canplay', onReady);
                  ldr.remove();
                });
                if (v.readyState >= 3) ldr.remove();
              }
            } else {
              v.pause();
            }
          }
        });
      }
    }
  });

  // 화살표 루프: 직접 제어
  document.querySelector('.depth-comparison-swiper .swiper-button-next').addEventListener('click', function() {
    if (depthSwiper.isEnd) depthSwiper.slideTo(0);
    else depthSwiper.slideNext();
  });
  document.querySelector('.depth-comparison-swiper .swiper-button-prev').addEventListener('click', function() {
    if (depthSwiper.isBeginning) depthSwiper.slideTo(depthSwiper.slides.length - 1);
    else depthSwiper.slidePrev();
  });

  // Auto-advance: cycle through slides, then switch baseline and repeat
  function bindDepthEnded() {
    var active = document.querySelector('.depth-comparison-swiper .swiper-slide-active .depth-vid');
    if (active) {
      active.onended = function() {
        if (depthSwiper.isEnd) depthSwiper.slideTo(0);
        else depthSwiper.slideNext();
      };
    }
  }
  bindDepthEnded();
  depthSwiper.on('slideChangeTransitionEnd', bindDepthEnded);

  // Baseline switcher
  var baselines = ['UniDepth', 'VDA'];
  var currentBaseline = 0;
  window.switchBaseline = function(dir) {
    currentBaseline = (currentBaseline + dir + baselines.length) % baselines.length;
    var label = document.querySelector('.depth-baseline-label');
    label.textContent = baselines[currentBaseline];

    document.querySelectorAll('.depth-vid').forEach(function(v) {
      var newSrc = currentBaseline === 0 ? v.dataset.uni : v.dataset.vda;
      var currentTime = v.currentTime;
      var wasPlaying = !v.paused;
      v.src = newSrc;
      v.load();
      v.addEventListener('loadeddata', function onLoad() {
        v.removeEventListener('loadeddata', onLoad);
        v.currentTime = currentTime;
        if (wasPlaying) v.play().catch(function(){});
      });
    });
  };
});
</script>

---

<div class="has-text-centered">
<h2>Citation</h2>
</div>

<div style="position: relative;">
<pre id="bibtex-code" style="background: #f5f5f5; padding: 16px 20px; border-radius: 8px; font-size: 0.85rem; overflow-x: auto;">@article{han2026ptcdepth,
  title={PTC-Depth: Pose-Refined Monocular Depth Estimation with Temporal Consistency},
  author={Han, Leezy and Kim, Seunggyu and Shim, Dongseok and Lee, Hyeonbeom},
  journal={arXiv preprint arXiv:2604.01791},
  year={2026}
}</pre>
<button onclick="copyBibtex()" style="position: absolute; top: 10px; right: 10px; background: #eee; border: 1px solid #ddd; border-radius: 6px; padding: 4px 10px; font-size: 0.75rem; cursor: pointer; color: #666;" id="copy-btn">Copy</button>
</div>

<script>
function copyBibtex() {
  var text = document.getElementById('bibtex-code').textContent;
  navigator.clipboard.writeText(text).then(function() {
    var btn = document.getElementById('copy-btn');
    btn.textContent = 'Copied!';
    btn.style.color = '#22ceb1';
    setTimeout(function() { btn.textContent = 'Copy'; btn.style.color = '#666'; }, 2000);
  });
}
</script>
