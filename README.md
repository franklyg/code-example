```javascript
  
    
  /*
    This function handles the scroll to top functionality
    Client wanted the ability to control the speed of the scroll to top functionality, so I added in some functions to control the speed and integrated Shopifys theming language to control the duration of the scroll speed.
  */
  function scrollIntoView(){
    const scrollTo = (element, duration) => {
      var e = document.documentElement;
      if(e.scrollTop===0){
          var t = e.scrollTop;
          ++e.scrollTop;
          e = t+1===e.scrollTop--?e:document.body;
      }
      scrollToC(e, e.scrollTop, element, duration);
    }

    const scrollToC = (element, from, to, duration) => {
      if (duration <= 0) return;
      if(typeof from === "object")from=from.offsetTop;
      if(typeof to === "object")to=to.offsetTop;

      scrollToX(element, from, to, 0, 1/duration, 20, easeOutCuaic);
    }

    const scrollToX = (element, xFrom, xTo, t01, speed, step, motion) => {
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
    const easeOutCuaic = (t) =>{
      t--;
      return t*t*t+1;
    }
    
    const topOfCollectionSection = document.querySelector('#collection-page-listing');
    const headerHeight = document.querySelector('.header-wrapper').clientHeight / 2;
    
    scrollTo(topOfCollectionSection.offsetTop - headerHeight, {{ scroll_speed }});
  }
  
  /*
    This function tracks the parameters on the page.
    With each pagination a parameter for the page is applied.
    This way if a user needs to navigate back, utlizing the browsers back button, or refreshes the page their page is in place with the correct parameters in place.
  */
  function pageNumberParamTracker(key, value) {
    const urlParams = new URLSearchParams(window.location.search);
    urlParams.set(key, value);
    const newRelativePathQuery = window.location.pathname + "?" + urlParams.toString();
    history.pushState(null, '', newRelativePathQuery);
  }
  
  /*
    This function initiates the call for the next or previous pages in the collection.
    I utilized datasets from the pagination and collection information that is required in order to trigger the pagination effect.
    I fetch the pages HTML string data from the linkURL that is placed in either the Next, Prev, or paged numbered buttons on click, which then proceeds to populate the ProductGrid.
    I have an operator to make sure if the button is the next button, it adds '+ 1' to the page interger so it opens the correct page. 
  */
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
