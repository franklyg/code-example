```
  function scrollTo(element, duration) {
    var e = document.documentElement;
    if(e.scrollTop===0){
        var t = e.scrollTop;
        ++e.scrollTop;
        e = t+1===e.scrollTop--?e:document.body;
    }
    scrollToC(e, e.scrollTop, element, duration);
  }
  
  // Element to move, element or px from, element or px to, time in ms to animate
  function scrollToC(element, from, to, duration) {
    if (duration <= 0) return;
    if(typeof from === "object")from=from.offsetTop;
    if(typeof to === "object")to=to.offsetTop;
  
    scrollToX(element, from, to, 0, 1/duration, 20, easeOutCuaic);
  }
  
  function scrollToX(element, xFrom, xTo, t01, speed, step, motion) {
    if (t01 < 0 || t01 > 1 || speed<= 0) {
        element.scrollTop = xTo;
        return;
    }
    element.scrollTop = xFrom - (xFrom - xTo) * motion(t01);
    t01 += speed * step;
  
    setTimeout(function() {
        scrollToX(element, xFrom, xTo, t01, speed, step, motion);
    }, step);
  }
  function easeOutCuaic(t){
    t--;
    return t*t*t+1;
  }

  function pageNumberParamTracker(key, value) {
    const urlParams = new URLSearchParams(window.location.search);
    urlParams.set(key, value);
    const newRelativePathQuery = window.location.pathname + "?" + urlParams.toString();
    history.pushState(null, '', newRelativePathQuery);
  }
  
  function scrollIntoView(){
    const topOfCollectionSection = document.querySelector('#collection-page-listing');
    const headerHeight = document.querySelector('.header-wrapper').clientHeight / 2;
    
    scrollTo(topOfCollectionSection.offsetTop - headerHeight, {{ scroll_speed }});
  }
  
  function nextPrevPageClickInit(e) {
    const productGrid = document.querySelector('.collection');
    const nextLink = document.querySelector('.pagination__item');
    let linkURL;
  
    const elem = e;
    
    const nextLinkData = elem.dataset.pageurl
    linkURL = nextLinkData;
  
    const pageNum = elem.dataset.pagenum
  
    const prevNextLink = elem.dataset.direction
    const collectionOverlay = document.querySelector('.collection .loading-overlay')
    collectionOverlay.style.display = 'block';
    fetch(linkURL, {
      method: 'GET',
    }).then((response) => response.text())
      .then((next_page) => {
      const parser = new DOMParser();
      const nextPageHTML = parser.parseFromString(next_page, 'text/html').querySelector('.collection');
      productGrid.innerHTML = nextPageHTML.innerHTML;
    });
  
    if (prevNextLink == 'next') {
      pageNumberParamTracker('page', parseInt(pageNum) + 1);
    } else {
      pageNumberParamTracker('page', pageNum);
    }

    scrollIntoView()
  }
  ```
