# painball
<!doctype html>
<meta charset=utf-8>
<meta name=viewport content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name=apple-mobile-web-app-capable content=yes>
<meta name=apple-mobile-web-app-status-bar-style content=black>
<title>phosphorus</title>
<link rel=stylesheet href=app.css>
<div class=player></div>
<div class=splash>
<div>
  <h1>phosphorus</h1>
  <div class=progress><div class=progress-bar></div></div>
</div>
</div>
<div class=error>
<div>
  <h1>phosphorus</h1>
  <p>An error has occurred. <a id=bug-link href=https://github.com/nathan/phosphorus/issues/new>Click here</a> to file a bug report on GitHub.</p>
</div>
</div>
<script src=fonts.js></script>
<script src=//cdnjs.cloudflare.com/ajax/libs/jszip/2.4.0/jszip.js></script>
<script src=//canvg.googlecode.com/svn/trunk/rgbcolor.js></script>
<script src=//canvg.googlecode.com/svn/trunk/StackBlur.js></script>
<script src=//canvg.googlecode.com/svn/trunk/canvg.js></script>
<script src=phosphorus.js></script>
<script>

(function () {
  'use strict';

  if (location.protocol === 'https:') {
    location.replace(('' + location).replace(/^https:/, 'http:'));
  }

  var stage;

  var projectId = 17088932;
  var projectTitle = '';
  var turbo = false;
  var fullScreen = true;

  var params = location.search.substr(1).split('&');
  params.forEach(function(p) {
    var parts = p.split('=');
    if (parts.length > 1) {
      switch (parts[0]) {
        case 'id':
          projectId = Number(parts[1]);
          break;
        case 'turbo':
          turbo = parts[1] !== 'false';
          break;
        case 'full-screen':
          fullScreen = parts[1] !== 'false';
          break;
      }
    }
  });

  var splash = document.querySelector('.splash');
  var progressBar = document.querySelector('.progress-bar');
  var error = document.querySelector('.error');
  var bugLink = document.querySelector('#bug-link');
  var player = document.querySelector('.player');

  var stage;

  function layout() {
    if (!stage) return;
    var w = Math.min(window.innerWidth, window.innerHeight / .75);
    if (!fullScreen) w = Math.min(w, 480);
    var h = w * .75;
    player.style.left = (window.innerWidth - w) / 2 + 'px';
    player.style.top = (window.innerHeight - h) / 2 + 'px';
    stage.setZoom(w / 480);
    stage.draw();
  }

  function showError(e) {
    error.style.display = 'table';
    bugLink.href = 'https://github.com/nathan/phosphorus/issues/new?title=' + encodeURIComponent(projectTitle || '') + '&body=' + encodeURIComponent('\n\n\nhttp://scratch.mit.edu/projects/' + projectId + '\nhttp://phosphorus.github.io/#' + projectId + (e.stack ? '\n\n```\n' + e.stack + '\n```' : ''));
    console.error(e.stack);
  }

  window.addEventListener('resize', layout);

  if (P.hasTouchEvents) {
    document.addEventListener('touchmove', function(e) {
      e.preventDefault();
    });
  }

  var request = P.IO.loadScratchr2Project(projectId);

  request.onload = function (s) {
    splash.style.display = 'none';

    stage = s;
    layout();

    s.isTurbo = turbo;
    s.start();
    s.triggerGreenFlag();

    player.appendChild(s.root);
    s.focus();
    s.handleError = showError;
  };
  request.onerror = showError;
  request.onprogress = function (e) {
    progressBar.style.width = (10 + e.loaded / e.total * 90) + '%';
  };

  P.IO.loadScratchr2ProjectTitle(projectId, function(title) {
    document.title = projectTitle = title;
  });

}());

</script>
