---
wrapper_template: "legal/_base_legal_markdown.html"
context:
  title: "Open-source licences"
---

# Ubuntu open-source licences

<aside>
  <nav class="p-side-navigation is-drawer-hidden" id="side-navigation-drawer" aria-label="Side">
    <a href="#side-navigation-drawer" class="p-side-navigation__toggle js-drawer-toggle" aria-controls="side-navigation-drawer" aria-expanded="false">
      Toggle side navigation
    </a>
    <div class="p-side-navigation__overlay js-drawer-toggle" aria-controls="side-navigation-drawer" aria-expanded="false"></div>
    <div class="p-side-navigation__drawer">
      <div class="p-side-navigation__drawer-header">
        <a href="#" class="p-side-navigation__toggle--in-drawer js-drawer-toggle" aria-controls="side-navigation-drawer" aria-expanded="false">
          Toggle side navigation
        </a>
      </div>
      <ul class="p-side-navigation__list" id="side-navigation">
        <li class="p-side-navigation__item--title"><span class="p-side-navigation__text">Releases</span></li>
        <li class="p-side-navigation__item">
          <a class="p-side-navigation__link" href="/legal/open-source-licences?release=jammy" data-file="56889593-licensing-jammy-base-install.json">
            22.04 LTS (Jammy Jellyfish)
          </a>
        </li>
        <li class="p-side-navigation__item">
          <a class="p-side-navigation__link" href="/legal/open-source-licences?release=focal" data-file="4f31fa98-licensing-focal-base-install.json">
            20.04 (Focal Fossa)
          </a>
        </li>
        <li class="p-side-navigation__item">
          <a class="p-side-navigation__link" href="/legal/open-source-licences?release=bionic" data-file="126e339c-licensing-bionic-base-install.json">
            18.04 LTS (Bionic Beaver)
          </a>
        </li>
        <li class="p-side-navigation__item">
          <a class="p-side-navigation__link" href="/legal/open-source-licences?release=xenial" data-file="4faf1157-licensing-xenial-base-install.json">
            16.04 LTS (Xenial Xerus)
          </a>
        </li>
        <li class="p-side-navigation__item">
          <a class="p-side-navigation__link" href="/legal/open-source-licences?release=trusty" data-file="ba9d51f0-licensing-trusty-base-install.json">
            14.04 LTS (Trusty Tahr)
          </a>
        </li>
      </ul>
    </div>

  </nav>
</aside>
<main class="col-9" id="main-content">
  <h2 class="u-hide--large" id="selection-title"></h2>
  <table class="p-table--mobile-card" aria-label="Open-source package licences">
    <thead>
      <tr>
        <th>Name</th>
        <th>Version</th>
        <th>Architecture</th>
        <th>Licenses</th>
      </tr>
    </thead>
    <caption id="loading">
      <div class="p-strip is-shallow">
        <div class="row">
          <div class="u-align--center col-8">
            <p><i class="p-icon--spinner u-animation--spin"></i> Loading package licences.</p>
          </div>
        </div>
      </div>
    </caption>
    <tbody id="content"></tbody>
    <caption class="u-hide" id="empty">
      <div class="p-strip">
        <div class="row">
          <div class="u-align--right col-3 col-medium-2 col-small-1">
            <img src="https://assets.ubuntu.com/v1/263e7c5d-iot_orange.svg" alt="">
          </div>
          <div class="u-align--left col-5 col-medium-3 col-small-3">
            <p class="p-heading--4 u-no-margin--bottom">No package information not available</p>
            <p>There are no package information for this release</p>
            <a href="https://github.com/canonical-websites/www.ubuntu.com/issues/new?body=%0a%0a%0a---%0a*Reported%20from:%20https:ubuntu.com/legal/open-source-licences*" class="p-button--positive">Let us know by reporting an issue</a>
          </div>
        </div>
      </div>
    </caption>
    <caption class="u-hide" id="error">
      <div class="p-strip">
        <div class="row">
          <div class="u-align--right col-3 col-medium-2 col-small-1">
            <img src="https://assets.ubuntu.com/v1/263e7c5d-iot_orange.svg" alt="">
          </div>
          <div class="u-align--left col-5 col-medium-3 col-small-3">
            <p class="p-heading--4 u-no-margin--bottom">Oh no, we hit a snag</p>
            <p>There was an error loading the package licence information</p>
            <a href="https://github.com/canonical-websites/www.ubuntu.com/issues/new?body=%0a%0a%0a---%0a*Reported%20from:%20https:ubuntu.com/legal/open-source-licences*" class="p-button--positive">Let us know by reporting an issue</a>
          </div>
        </div>
      </div>
    </caption>
  </table>
</main>

<script>
  const elements = {
    content: document.getElementById("content"),
    empty: document.getElementById("empty"),
    loading: document.getElementById("loading"),
    error: document.getElementById("error"),
    navigation: document.getElementById("side-navigation"),
    title: document.getElementById("selection-title"),
  };

  document.addEventListener('DOMContentLoaded', function() {
    const urlParams = new URLSearchParams(window.location.search);
    const release = urlParams.get('release');

    const jsonFile = getJsonFile(release);

    if (jsonFile) {
      fetch(`/asset/${jsonFile}`)
        .then(response => response.json())
        .then(json => {
          try {
            elements.loading.classList.add("u-hide");
            const packages = json.Packages;
            renderTable(packages)
          }
          catch(error) {
            handleError(error)
          }
        })
        .catch((error) => {
          handleError(error)
        });
    } else {
      handleError(`No release file found for "${release}"`);
    }
  });

  function getJsonFile(release) {
    let file = null;
    if (release) {
      const links = elements.navigation.querySelectorAll('.p-side-navigation__link');
      links.forEach(link => {
        if (link.href.split("?release=")[1] === release.toLowerCase()) {
          link.setAttribute("aria-current", "page");
          elements.title.innerText = link.innerText.trim();
          file = link.dataset.file;
        }
      });
    } else {
      const link = elements.navigation.querySelector('.p-side-navigation__link');
      link.setAttribute("aria-current", "page");
      file = link.dataset.file;
    }
    return file;
  }

  function renderTable(json) {
    if (json.length === 0) {
      elements.empty.classList.remove("u-hide");
      return;
    }
    const rows = renderTableRows(json);
    elements.content.innerHTML = rows;
  }

  function renderTableRows(packages) {
    let rows = "";
    packages.forEach(function(package) {
      let licence = ''
      if (package.copyright_url && package.licenses) {
        licence = `<td data-heading="Licenses"><a href="${package.copyright_url}">${package.licenses}</a></td>`;
      }
      rows += `<tr>
        <td data-heading="Name">${package.name}</td>
        <td data-heading="Version">${package.version}</td>
        <td data-heading="Architecture">${package.component}</td>
        ${licence}
      </tr>`;
    });
    return rows;
  }

  function handleError(error) {
    console.log(error);
    elements.loading.classList.add("u-hide");
    elements.error.classList.remove("u-hide");
  }
</script>

<script>
  /**
  Toggles the expanded/collapsed classed on side navigation element.

  @param {HTMLElement} sideNavigation The side navigation element.
  @param {Boolean} show Whether to show or hide the drawer.
*/
function toggleDrawer(sideNavigation, show) {
  const toggleButtonOutsideDrawer = sideNavigation.querySelector('.p-side-navigation__toggle');
  const toggleButtonInsideDrawer = sideNavigation.querySelector('.p-side-navigation__toggle--in-drawer');

  if (sideNavigation) {
    if (show) {
      sideNavigation.classList.remove('is-drawer-collapsed');
      sideNavigation.classList.add('is-drawer-expanded');

      toggleButtonInsideDrawer.focus();
      toggleButtonOutsideDrawer.setAttribute('aria-expanded', true);
      toggleButtonInsideDrawer.setAttribute('aria-expanded', true);
    } else {
      sideNavigation.classList.remove('is-drawer-expanded');
      sideNavigation.classList.add('is-drawer-collapsed');

      toggleButtonOutsideDrawer.focus();
      toggleButtonOutsideDrawer.setAttribute('aria-expanded', false);
      toggleButtonInsideDrawer.setAttribute('aria-expanded', false);
    }
  }
}

// throttle util (for window resize event)
var throttle = function (fn, delay) {
  var timer = null;
  return function () {
    var context = this,
      args = arguments;
    clearTimeout(timer);
    timer = setTimeout(function () {
      fn.apply(context, args);
    }, delay);
  };
};

/**
  Attaches event listeners for the side navigation toggles
  @param {HTMLElement} sideNavigation The side navigation element.
*/
function setupSideNavigation(sideNavigation) {
  var toggles = [].slice.call(sideNavigation.querySelectorAll('.js-drawer-toggle'));
  var drawerEl = sideNavigation.querySelector('.p-side-navigation__drawer');

  // hide navigation drawer on small screens
  sideNavigation.classList.add('is-drawer-hidden');

  // setup drawer element
  drawerEl.addEventListener('animationend', () => {
    if (!sideNavigation.classList.contains('is-drawer-expanded')) {
      sideNavigation.classList.add('is-drawer-hidden');
    }
  });

  window.addEventListener('keydown', (e) => {
    if (e.key === 'Escape') {
      toggleDrawer(sideNavigation, false);
    }
  });

  // setup toggle buttons
  toggles.forEach(function (toggle) {
    toggle.addEventListener('click', function (event) {
      event.preventDefault();

      if (sideNavigation) {
        sideNavigation.classList.remove('is-drawer-hidden');
        toggleDrawer(sideNavigation, !sideNavigation.classList.contains('is-drawer-expanded'));
      }
    });
  });

  // hide side navigation drawer when screen is resized
  window.addEventListener(
    'resize',
    throttle(function () {
      toggles.forEach((toggle) => {
        return toggle.setAttribute('aria-expanded', false);
      });
      // remove expanded/collapsed class names to avoid unexpected animations
      sideNavigation.classList.remove('is-drawer-expanded');
      sideNavigation.classList.remove('is-drawer-collapsed');
      sideNavigation.classList.add('is-drawer-hidden');
    }, 10)
  );
}

/**
  Attaches event listeners for all the side navigations in the document.
  @param {String} sideNavigationSelector The CSS selector matching side navigation elements.
*/
function setupSideNavigations(sideNavigationSelector) {
  // Setup all side navigations on the page.
  var sideNavigations = [].slice.call(document.querySelectorAll(sideNavigationSelector));

  sideNavigations.forEach(setupSideNavigation);
}

setupSideNavigations('.p-side-navigation, [class*="p-side-navigation--"]');
</script>
