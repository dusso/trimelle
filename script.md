<script>  
  const tlLang = "{{ request.locale.iso_code }}";  
  
  // ─── TRADUÇÕES: FR (default) + DE ────────────────────────────────────────  
  const tlT = {  
    // Carrinho  
    cartTitle: tlLang === 'de' ? 'Ihr Warenkorb' : 'Votre Panier',  
    cartEmpty: tlLang === 'de' ? 'Ihr Warenkorb ist leer.' : 'Votre panier est vide.',  
    loading: tlLang === 'de' ? 'Wird geladen...' : 'Chargement...',  
    detailsPayment: tlLang === 'de' ? 'ZAHLUNGSDETAILS' : 'DÉTAILS DU PAIEMENT',  
    gratuite: tlLang === 'de' ? 'Gratis' : 'Gratuite',  
    bundle1: tlLang === 'de' ? '1 Stück' : '1 pièce',  
    bundle2: tlLang === 'de' ? '3 Stück' : '3 pièces',  
    bundle3: tlLang === 'de' ? '5 Stück' : '5 pièces',  
  
    // Modal delete  
    deleteBundle: tlLang === 'de' ? 'Durch das Löschen dieses Bundles verlieren Sie auch den zugehörigen Rabatt.' : 'En supprimant ce bundle, vous perdrez aussi la réduction associée.',  
    deleteSingle: tlLang === 'de' ? 'Diesen Artikel aus dem Warenkorb entfernen?' : 'Supprimer cet article du panier ?',  
    irreversible: tlLang === 'de' ? 'Diese Aktion kann nicht rückgängig gemacht werden.' : 'Cette action est irréversible.',  
    confirmYes: tlLang === 'de' ? 'Ja, löschen' : 'Oui, supprimer',  
    confirmNo: tlLang === 'de' ? 'Nein' : 'Non',  
  
    // Botão checkout  
    checkout: tlLang === 'de' ? 'Zur Kasse' : 'Procéder au Paiement',  
  
    // Toast / add to cart  
    addedToCart: tlLang === 'de' ? 'Produkt wurde hinzugefügt.' : 'Produit ajouté à votre panier.',  
    addError: tlLang === 'de' ? 'Fehler beim Hinzufügen.' : 'Erreur lors de l\'ajout au panier.',  
    networkError: tlLang === 'de' ? 'Netzwerkfehler.' : 'Erreur réseau.',  
    selectSize: tlLang === 'de' ? 'Bitte wählen Sie eine Größe.' : 'Veuillez sélectionner une taille.',  
    adding: tlLang === 'de' ? 'Wird hinzugefügt...' : 'Ajout en cours...',  
    addedBtn: tlLang === 'de' ? '✓ Hinzugefügt!' : '✓ Ajouté au panier !',  
    addRetry: tlLang === 'de' ? 'Fehler — Erneut versuchen' : 'Erreur — Réessayez',  
    notFound: tlLang === 'de' ? 'Kombination nicht gefunden: ' : 'Combinaison introuvable: ',  
  
    // Newsletter  
    newsletterOk: tlLang === 'de' ? 'Danke für Ihre Anmeldung!' : 'Merci pour votre inscription !',  
  };  
  
  
  // ─── CARROSSEL ───────────────────────────────────────────────────────────  
  
  let tlCarouselIndex = 0;  
  
  function tlCarouselGeometry() {  
    const carousel = document.getElementById('tl-carousel');  
    if (!carousel) return null;  
  
    const wrapper = carousel.parentElement;  
    const slides = Array.from(carousel.children).filter(  
      slide => !slide.hidden  
    );  
  
    if (!slides.length) {  
      return { carousel, wrapper, slides, stops: [0] };  
    }  
  
    const firstLeft = slides[0].offsetLeft;  
    const last = slides[slides.length - 1];  
  
    const contentWidth =  
      last.offsetLeft - firstLeft + last.offsetWidth;  
  
    // Respiro depois da última foto, apenas no mobile.  
    const maxOffset = Math.max(  
      0,  
      contentWidth - wrapper.clientWidth + 24  
    );  
  
    const stops = slides.map((slide, index) => {  
      if (index === slides.length - 1) return maxOffset;  
  
      return Math.min(  
        slide.offsetLeft - firstLeft,  
        maxOffset  
      );  
    });  
  
    return { carousel, wrapper, slides, stops };  
  }  
  
  function tlCarousel(i) {  
    const geometry = tlCarouselGeometry();  
    if (!geometry) return;  
  
    const { carousel, slides, stops } = geometry;  
  
    tlCarouselIndex = Math.max(  
      0,  
      Math.min(Math.max(0, slides.length - 1), i)  
    );  
  
    const mobile = window.matchMedia(  
      '(max-width: 767px)'  
    ).matches;  
  
    const reducedMotion = window.matchMedia(  
      '(prefers-reduced-motion: reduce)'  
    ).matches;  
  
    carousel.style.transition = mobile && !reducedMotion  
      ? 'transform 320ms cubic-bezier(0.22, 1, 0.36, 1)'  
      : 'none';  
  
    carousel.style.transform = mobile  
      ? `translate3d(-${stops[tlCarouselIndex] || 0}px, 0, 0)`  
      : 'translate3d(0, 0, 0)';  
  
    const dots = document.getElementById('tl-carousel-dots');  
  
    if (dots) {  
      dots.querySelectorAll('button').forEach((dot, j) => {  
        dot.className =  
          'w-2 h-2 rounded-full transition-all ' +  
          (j === tlCarouselIndex  
            ? 'bg-choc-900'  
            : 'bg-choc-900/30');  
      });  
    }  
  }  
  
  window.addEventListener('resize', function () {  
    tlCarousel(tlCarouselIndex);  
  });  
  
  (function () {  
    const carousel = document.getElementById('tl-carousel');  
    if (!carousel) return;  
  
    const wrapper = carousel.parentElement;  
    if (!wrapper) return;  
  
    let gesture = null;  
  
    function currentOffset() {  
      const transform = getComputedStyle(carousel).transform;  
  
      if (!transform || transform === 'none') return 0;  
  
      return -new DOMMatrixReadOnly(transform).m41;  
    }  
  
    wrapper.addEventListener('touchstart', event => {  
      if (  
        !window.matchMedia('(max-width: 767px)').matches ||  
        event.touches.length !== 1  
      ) {  
        gesture = null;  
        return;  
      }  
  
      const geometry = tlCarouselGeometry();  
      if (!geometry || geometry.slides.length < 2) return;  
  
      const offset = currentOffset();  
  
      carousel.style.transition = 'none';  
      carousel.style.transform =  
        `translate3d(-${offset}px, 0, 0)`;  
  
      gesture = {  
        x: event.touches[0].clientX,  
        y: event.touches[0].clientY,  
        offset,  
        current: offset,  
        direction: null,  
        geometry  
      };  
    }, { passive: true });  
  
    wrapper.addEventListener('touchmove', event => {  
      if (!gesture || event.touches.length !== 1) return;  
  
      const dx = event.touches[0].clientX - gesture.x;  
      const dy = event.touches[0].clientY - gesture.y;  
  
      if (!gesture.direction) {  
        if (Math.max(Math.abs(dx), Math.abs(dy)) < 6) return;  
  
        gesture.direction =  
          Math.abs(dx) > Math.abs(dy) ? 'horizontal' : 'vertical';  
      }  
  
      if (gesture.direction !== 'horizontal') return;  
  
      if (event.cancelable) event.preventDefault();  
  
      const stops = gesture.geometry.stops;  
      const max = stops[stops.length - 1];  
  
      let offset = gesture.offset - dx;  
  
      // Resistência suave nas extremidades.  
      if (offset < 0) {  
        offset *= 0.2;  
      } else if (offset > max) {  
        offset = max + (offset - max) * 0.2;  
      }  
  
      gesture.current = offset;  
  
      carousel.style.transform =  
        `translate3d(-${offset}px, 0, 0)`;  
    }, { passive: false });  
  
    function finishGesture(cancelled) {  
      if (!gesture) return;  
  
      const finished = gesture;  
      gesture = null;  
  
      if (finished.direction !== 'horizontal') {  
        tlCarousel(tlCarouselIndex);  
        return;  
      }  
  
      const stops = finished.geometry.stops;  
  
      let nearest = 0;  
  
      stops.forEach((stop, index) => {  
        if (  
          Math.abs(stop - finished.current) <  
          Math.abs(stops[nearest] - finished.current)  
        ) {  
          nearest = index;  
        }  
      });  
  
      const distance = finished.current - finished.offset;  
  
      // Um arrasto curto também pode avançar uma foto.  
      if (  
        !cancelled &&  
        nearest === tlCarouselIndex &&  
        Math.abs(distance) > 30  
      ) {  
        nearest += distance > 0 ? 1 : -1;  
      }  
  
      tlCarousel(nearest);  
    }  
  
    wrapper.addEventListener('touchend', () => {  
      finishGesture(false);  
    }, { passive: true });  
  
    wrapper.addEventListener('touchcancel', () => {  
      finishGesture(true);  
    }, { passive: true });  
  
    document.addEventListener('tl:selection-change', () => {  
      const selection =  
        window.tlProgressiveSelection?.getSelection();  
  
      if (  
        selection &&  
        gesture &&  
        gesture.geometry.slides[0]?.dataset.galleryColor !==  
          selection.color  
      ) {  
        gesture = null;  
      }  
    });  
  })();  
  
  
  // ─── BUNDLE LOGIC ────────────────────────────────────────────────────────  
  
  const tlVariantMap = {  
    'S-Black': '64091790147933',  
    'S-Beige': '64091790180701',  
    'M-Black': '64091790213469',  
    'M-Beige': '64091790246237',  
    'L-Black': '64091790279005',  
    'L-Beige': '64091790311773',  
    'XL-Black': '64091790344541',  
    'XL-Beige': '64091790377309',  
    'XXL-Black': '64091790410077',  
    'XXL-Beige': '64091790442845'  
  };  
  
  const tlBundleConfig = {  
    1: { totalPieces: 1, paidPieces: 1, freePieces: 0, totalPrice: 29.99, savings: 0 },  
    2: { totalPieces: 3, paidPieces: 2, freePieces: 1, totalPrice: 59.98, savings: 29.99 },  
    3: { totalPieces: 5, paidPieces: 3, freePieces: 2, totalPrice: 89.97, savings: 59.98 }  
  };  
  
  let tlCurrentBundle = 2;  
  let tlBundleItems = [  
    { color: 'Beige', size: 'M' },  
    { color: 'Black', size: 'M' },  
    { color: 'Beige', size: 'M' },  
    { color: 'Black', size: 'L' },  
    { color: 'Beige', size: 'L' }  
  ];  
  
  function tlSelectBundle(id) {  
    if (tlCurrentBundle === id) {  
      const panel = document.getElementById(`tl-panel-${id}`);  
      const chevron = document.getElementById(`tl-chevron-${id}`);  
      panel.style.display = 'none';  
      chevron.classList.remove('rotate-180');  
      tlCurrentBundle = 0;  
      return;  
    }  
  
    tlCurrentBundle = id;  
  
    for (let i = 1; i <= 3; i++) {  
      const check = document.getElementById(`tl-check-${i}`);  
      const chevron = document.getElementById(`tl-chevron-${i}`);  
      const panel = document.getElementById(`tl-panel-${i}`);  
      const card = document.getElementById(`tl-bundle-card-${i}`);  
  
      if (i === id) {  
        check.className = 'w-5 h-5 rounded-full bg-choc-500 text-cream-50 flex items-center justify-center text-xs font-bold';  
        check.innerText = '✓';  
        chevron.classList.add('rotate-180');  
        panel.style.display = 'block';  
        card.classList.add('border-choc-500', 'bg-cream-200/80');  
        card.classList.remove('border-cream-300', 'bg-cream-50');  
        tlRenderItems(i);  
        tlRenderSummary(i);  
      } else {  
        check.className = 'w-5 h-5 rounded-full border border-cream-400 flex items-center justify-center text-cream-50 text-xs';  
        check.innerText = '';  
        chevron.classList.remove('rotate-180');  
        panel.style.display = 'none';  
        card.classList.add('border-cream-300', 'bg-cream-50');  
        card.classList.remove('border-choc-500', 'bg-cream-200/80');  
      }  
    }  
  }  
  
  
  function tlRenderItems(bundleId) {  
    const config = tlBundleConfig[bundleId];  
    const sizes = ['S', 'M', 'L', 'XL', 'XXL'];  
    const container = document.getElementById(`tl-items-${bundleId}`);  
    const isDE = tlLang === 'de';  
    let html = '';  
  
    for (let i = 0; i < config.totalPieces; i++) {  
      const isFree = i >= config.paidPieces;  
      const item = tlBundleItems[i];  
      html += `  
      <div class="p-3.5 rounded-lg bg-cream-50 border border-cream-300 space-y-2.5">  
        <div class="flex items-center justify-between">  
          <span class="text-sm font-bold text-choc-900 flex items-center gap-2">  
            ${isDE ? 'Stück' : 'Pièce'} ${i + 1}  
            ${isFree ? `<span class="bg-choc-500 text-cream-50 text-[9px] font-bold tracking-wider uppercase px-2 py-0.5 rounded">${isDE ? 'GRATIS' : 'GRATUITE'}</span>` : ''}  
          </span>  
        </div>  
        <div class="space-y-1.5 pt-1">  
          <div class="flex items-center justify-between text-[11px] text-choc-700/70">  
            <span>${isDE ? 'Farbe :' : 'Couleur :'}</span>  
            <div class="flex space-x-2">  
              <button type="button" onclick="tlSetColor(${i},'Beige')" class="flex items-center space-x-1 border ${item.color === 'Beige' ? 'border-choc-500 bg-cream-200' : 'border-cream-300'} px-2 py-1 rounded text-[10px]">  
                <span class="w-3 h-3 rounded-full border border-choc-700/20" style="background:#E3CBB5"></span>  
                <span>Beige</span>  
              </button>  
              <button type="button" onclick="tlSetColor(${i},'Black')" class="flex items-center space-x-1 border ${item.color === 'Black' ? 'border-choc-500 bg-cream-200' : 'border-cream-300'} px-2 py-1 rounded text-[10px]">  
                <span class="w-3 h-3 rounded-full" style="background:#1A1A1A"></span>  
                <span>${isDE ? 'Schwarz' : 'Noir'}</span>  
              </button>  
            </div>  
          </div>  
          <div class="space-y-1 pt-1">  
            <span class="text-[11px] text-choc-700/70 block">${isDE ? 'Größe :' : 'Taille :'}</span>  
            <div class="grid grid-cols-6 gap-1">  
              ${sizes.map(s => `  
                <button type="button" onclick="tlSetSize(${i},'${s}')" class="py-1.5 text-[11px] font-medium rounded border transition-colors ${item.size === s ? 'bg-transparent text-choc-900 border-choc-900 font-semibold text-[13px]' : 'text-[12px] bg-cream-50 text-choc-900 border-cream-300 hover:border-choc-700/40'}">  
                  ${s}  
                </button>  
              `).join('')}  
            </div>  
            <div class="text-right mt-1">  
              <button type="button" onclick="openSizeModal()" class="text-[11px] text-choc-500 underline underline-offset-2 hover:text-choc-900 transition-colors">  
                ${isDE ? 'Größentabelle' : 'Guide des Tailles'}  
              </button>  
            </div>  
          </div>  
        </div>  
      </div>`;  
    }  
  
    container.innerHTML = html;  
  }  
  
  function tlRenderSummary(bundleId) {  
    const config = tlBundleConfig[bundleId];  
    const container = document.getElementById(`tl-summary-${bundleId}`);  
    if (!container) return;  
    const isDE = tlLang === 'de';  
  
    const grouped = {};  
    for (let i = 0; i < config.totalPieces; i++) {  
      const item = tlBundleItems[i];  
      const isFree = i >= config.paidPieces;  
      const key = `${item.size}-${item.color}-${isFree}`;  
      if (!grouped[key]) grouped[key] = { size: item.size, color: item.color, isFree, count: 0 };  
      grouped[key].count++;  
    }  
  
    container.innerHTML = Object.values(grouped).map(g => `  
      <div class="flex items-center gap-1.5 text-[11px] text-choc-700/80">  
        <span class="text-choc-400">•</span>  
        <span>${g.count}x ${g.size} — ${g.color === 'Beige' ? 'Beige' : (isDE ? 'Schwarz' : 'Noir')}${g.isFree ? ` <span class="text-choc-500 font-medium">(${isDE ? 'Gratis' : 'Gratuite'})</span>` : ''}</span>  
      </div>  
    `).join('');  
  }  
  
  function tlSetColor(index, color) {  
    tlBundleItems[index].color = color;  
    tlRenderItems(tlCurrentBundle);  
    tlRenderSummary(tlCurrentBundle);  
  }  
  
  function tlSetSize(index, size) {  
    tlBundleItems[index].size = size;  
    tlRenderItems(tlCurrentBundle);  
    tlRenderSummary(tlCurrentBundle);  
  }  
  
  // ─── SINGLE PRODUCT (legacy) ─────────────────────────────────────────────  
  
  let tlColor = 'Beige';  
  let tlSize = 'S';  
  
  const tlVariants = {  
    {% for variant in featured_product.variants %}  
      {{ variant.option1 | append: '-' | append: variant.option2 | json }}: {{ variant.id | json }}{% unless forloop.last %},{% endunless %}  
    {% endfor %}  
  };  
  
  function tlGetVariantId() {  
    return tlVariants[tlSize + '-' + tlColor] || tlVariants[Object.keys(tlVariants)[0]];  
  }  
  
  function tlSetImage(src) {  
    document.getElementById('tl-main-img').src = src;  
  }  
  
  function tlSelectColor(label, key, imgSrc, btn) {  
    tlColor = key;  
    document.getElementById('tl-color-label').innerText = label;  
    tlSetImage(imgSrc);  
    document.querySelectorAll('.tl-color-btn').forEach(b => {  
      b.classList.remove('border-choc-800');  
      b.classList.add('border-transparent');  
    });  
    btn.classList.add('border-choc-800');  
    btn.classList.remove('border-transparent');  
  }  
  
  function tlSelectSize(size, btn) {  
    tlSize = size;  
    document.getElementById('tl-size-label').innerText = size;  
    document.querySelectorAll('.tl-size-btn').forEach(b => {  
      b.className = 'tl-size-btn py-2 text-xs font-medium border border-cream-300 hover:border-choc-800 rounded-sm transition';  
    });  
    btn.className = 'tl-size-btn py-2 text-xs font-medium border-2 border-choc-800 bg-choc-900 text-cream-50 rounded-sm transition';  
  }  
  
  // ─── SINGLE PRODUCT: tlAddToCart ─────────────────────────────────────────  
let tlAdding = false;  
  
  
function tlRenderAddButton() {  
  const button = document.getElementById('tl-progressive-add');  
  const subtotal = document.getElementById(  
    'tl-progressive-selection-subtotal'  
  );  
  
  if (  
    !button ||  
    !subtotal ||  
    button.getAttribute('aria-busy') === 'true'  
  ) return;  
  
  const isDE = tlLang === 'de';  
  
  const label = document.createElement('span');  
  label.className = 'tl-add-label';  
  label.textContent = isDE  
    ? 'In den Warenkorb'  
    : 'Ajouter au panier';  
  
  const separator = document.createElement('span');  
  separator.className = 'tl-add-separator';  
  separator.textContent = '|';  
  separator.setAttribute('aria-hidden', 'true');  
  
  const amount = document.createElement('span');  
  amount.className = 'tl-add-amount';  
  amount.textContent = subtotal.textContent.trim();  
  
  button.replaceChildren(label, separator, amount);  
  
  button.title = isDE  
    ? 'Geschätzte Summe dieser Auswahl. Der endgültige Rabatt wird im Warenkorb bestätigt.'  
    : 'Sous-total estimé de cette sélection. La remise définitive est confirmée dans le panier.';  
}  
  
  
  
async function tlAddToCart() {  
  if (tlAdding) return;  
  
  const selection =  
    window.tlProgressiveSelection?.getSelection();  
  
  const button = document.getElementById('tl-progressive-add');  
  const error = document.getElementById('tl-progressive-error');  
  
  if (!selection || !button || !error) return;  
  
  const variant = selection.variant;  
  const quantity = selection.quantity;  
  const isDE = tlLang === 'de';  
  
  function showError(message) {  
    error.textContent = message;  
    error.classList.remove('hidden');  
  }  
  
  if (!variant || !variant.available) {  
    showError(isDE  
      ? 'Diese Kombination ist nicht verfügbar.'  
      : 'Cette combinaison est indisponible.');  
    return;  
  }  
  
  if (!Number.isSafeInteger(quantity) || quantity < 1) {  
    showError(isDE  
      ? 'Bitte wählen Sie eine gültige Menge.'  
      : 'Veuillez choisir une quantité valide.');  
    return;  
  }  
  
  tlAdding = true;  
  button.disabled = true;  
  button.setAttribute('aria-busy', 'true');  
  button.textContent = tlT.adding;  
  error.classList.add('hidden');  
  
  const routeRoot = window.Shopify?.routes?.root || '/';  
  let confirmed = false;  
  
  try {  
    const response = await fetch(`${routeRoot}cart/add.js`, {  
      method: 'POST',  
      headers: {  
        'Content-Type': 'application/json',  
        'Accept': 'application/json'  
      },  
      body: JSON.stringify({  
        items: [{  
          id: variant.id,  
          quantity: quantity  
        }]  
      })  
    });  
  
    const result = await response.json();  
  
    if (!response.ok) {  
      const message =  
        typeof result.description === 'string'  
          ? result.description  
          : typeof result.message === 'string'  
            ? result.message  
            : tlT.addError;  
  
      throw new Error(message);  
    }  
  
    confirmed = true;  
    tlShowToast(tlT.addedToCart);  
    toggleCart(true);  
  } catch (errorCaught) {  
    showError(  
      errorCaught instanceof TypeError  
        ? (isDE  
          ? 'Verbindung unterbrochen. Prüfen Sie den Warenkorb, bevor Sie es erneut versuchen.'  
          : 'Connexion interrompue. Vérifiez le panier avant de réessayer.')  
        : errorCaught.message || tlT.addError  
    );  
  } finally {  
    // Atualizar também após erro: conferir o estado real do carrinho.  
    await tlLoadCart();  
  
    tlAdding = false;  
    button.removeAttribute('aria-busy');  
  
    const currentSelection =  
      window.tlProgressiveSelection?.getSelection();  
  
    button.disabled = !currentSelection?.variant?.available;  
  
    tlRenderAddButton();  
  
  
    if (confirmed) {  
      document.dispatchEvent(new CustomEvent('tl:cart-updated'));  
    }  
  }  
}  
  
  
  // ─── CART: tlAddBundleToCart ──────────────────────────────────────────────  
  
  async function tlAddBundleToCart() {  
    const config = tlBundleConfig[tlCurrentBundle];  
    const btn = document.getElementById(`tl-btn-${tlCurrentBundle}`);  
    const btnTxt = document.getElementById(`tl-btn-text-${tlCurrentBundle}`);  
    const bundleId = `b_${Date.now()}`;  
  
    const items = [];  
    for (let i = 0; i < config.totalPieces; i++) {  
      const item = tlBundleItems[i];  
      const key = `${item.size}-${item.color}`;  
      const variantId = tlVariantMap[key];  
      if (!variantId) { alert(tlT.notFound + key); return; }  
      items.push({  
        id: variantId,  
        quantity: 1,  
        properties: {  
          _bundle_id: bundleId,  
          _bundle_type: String(tlCurrentBundle),  
          _bundle_index: String(i),  
          _is_free: i >= config.paidPieces ? 'true' : 'false'  
        }  
      });  
    }  
  
    btn.disabled = true;  
    btnTxt.innerText = tlT.adding;  
  
    try {  
      const res = await fetch('/cart/add.js', {  
        method: 'POST',  
        headers: { 'Content-Type': 'application/json' },  
        body: JSON.stringify({ items })  
      });  
  
      if (!res.ok) throw new Error('Erreur cart');  
  
      btn.style.background = '#166534';  
      btnTxt.innerText = tlT.addedBtn;  
      setTimeout(() => { toggleCart(true); tlLoadCart(); }, 400);  
  
      setTimeout(() => {  
        btn.style.background = '';  
        btnTxt.innerText = config.btnText || tlT.addedBtn;  
        btn.disabled = false;  
      }, 2500);  
  
    } catch (e) {  
      btnTxt.innerText = tlT.addRetry;  
      btn.disabled = false;  
      setTimeout(() => { btnTxt.innerText = config.btnText || tlT.addedBtn; }, 2000);  
    }  
  }  
  
function tlCartImage(item) {  
  function normalizeColor(value) {  
    const color = String(value || '').trim().toLowerCase();  
  
    if (['black', 'schwarz', 'noir'].includes(color)) {  
      return 'Black';  
    }  
  
    if (color === 'beige') return 'Beige';  
  
    return color;  
  }  
  
  const dataElement = document.getElementById(  
    'tl-progressive-product-data'  
  );  
  
  if (dataElement) {  
    const product = JSON.parse(dataElement.textContent);  
  
    if (String(item.product_id) === String(product.id)) {  
      const variant = (product.variants || []).find(v =>  
        String(v.id) === String(item.variant_id)  
      );  
  
      if (variant) {  
        const color = normalizeColor(variant.option2);  
  
        const slides = document.querySelectorAll(  
          '#tl-carousel > [data-gallery-color]'  
        );  
  
        const matchingSlide = Array.from(slides).find(slide =>  
          normalizeColor(slide.dataset.galleryColor) === color  
        );  
  
        const image = matchingSlide?.querySelector('img');  
  
        if (image) {  
          const source =  
            image.currentSrc ||  
            image.getAttribute('src');  
  
          if (source) return source;  
        }  
      }  
    }  
  }  
  
  return item.featured_image?.url || item.image || '';  
}  
  
  
  
  // ─── CART: tlLoadCart ─────────────────────────────────────────────────────  
  async function tlLoadCart() {  
    try {  
      const res = await fetch('/cart.js');  
      const cart = await res.json();  
      window.tlProgressiveCart = cart;  
  
      document.dispatchEvent(new CustomEvent('tl:cart-data', {  
        detail: cart  
      }));  
  
      // -- Título do drawer traduzido --  
      const cartTitle = document.getElementById('tl-cart-title');  
      if (cartTitle) cartTitle.innerText = tlT.cartTitle;  
  
      // -- Label do botão checkout traduzido --  
      const checkoutLabel = document.getElementById('tl-checkout-label');  
      if (checkoutLabel) checkoutLabel.innerText = tlT.checkout;  
  
      const body = document.getElementById('tl-cart-body');  
      const total = document.getElementById('tl-cart-total');  
      const breakdown = document.getElementById('tl-cart-breakdown');  
  
      // -- Badge FAB --  
      const fabCount = document.getElementById('tl-cart-fab-count');  
      if (fabCount) {  
        if (cart.item_count > 0) {  
          fabCount.innerText = cart.item_count;  
          fabCount.classList.remove('hidden');  
          fabCount.classList.add('flex');  
        } else {  
          fabCount.classList.remove('flex');  
          fabCount.classList.add('hidden');  
        }  
      }  
  
      // -- Carrinho vazio --  
      if (cart.items.length === 0) {  
        body.innerHTML = `<p class="text-sm text-choc-700/60 font-light text-center mt-8">${tlT.cartEmpty}</p>`;  
        if (breakdown) breakdown.innerHTML = '';  
        total.innerText = '0.00 CHF';  
        const rEl = document.getElementById('tl-cart-reduction');  
        if (rEl) rEl.classList.add('hidden');  
        return;  
      }  
  
      // -- Separar bundles de singles --  
      const bundles = {};  
      const singles = [];  
      cart.items.forEach(item => {  
        const bid = item.properties && item.properties._bundle_id;  
        if (bid) {  
          if (!bundles[bid]) bundles[bid] = [];  
          bundles[bid].push(item);  
        } else {  
          singles.push(item);  
        }  
      });  
  
      // ── ORDER SUMMARY ────────────────────────────────────────────────────  
  
      const bundleLabels = { 1: tlT.bundle1, 2: tlT.bundle2, 3: tlT.bundle3 };  
      let bodyHtml = '';  
  
      // -- Bundles --  
      Object.entries(bundles).forEach(([bid, items]) => {  
        const bundleType = parseInt(items[0].properties._bundle_type);  
        const allKeys = items.map(i => i.key);  
  
        bodyHtml += `  
          <div class="mb-4 rounded-xl border border-cream-300 bg-cream-50 overflow-hidden">  
            <div class="px-4 py-3 border-b border-cream-200 flex justify-between items-center">  
              <span class="tl-serif text-base text-choc-900 font-medium">Bundle ${bundleLabels[bundleType] || bundleType}</span>  
            </div>  
            ${items.map(item => {  
          const isFree = item.properties._is_free === 'true';  
          return `  
                <div class="flex items-center gap-3 px-4 py-3 border-b border-cream-200 last:border-0">  
                  <img src="${tlCartImage(item)}" alt="${item.variant_title}"  
                    class="w-14 h-16 object-cover rounded-sm border border-cream-300 flex-shrink-0">  
                  <div class="flex-1 min-w-0">  
                    <p class="text-sm text-choc-900 font-medium leading-tight truncate">${item.product_title}</p>  
                    <p class="text-xs text-choc-700/70 mt-0.5">${item.variant_title}</p>  
                    ${isFree  
              ? `<span class="inline-block mt-1 text-[9px] bg-choc-500 text-cream-50 px-1.5 py-0.5 rounded font-bold uppercase tracking-wider">${tlT.gratuite}</span>`  
              : `<p class="text-xs font-semibold text-choc-900 mt-1">${(item.original_price / 100).toFixed(2)} CHF</p>`  
            }  
                  </div>  
                  <div class="flex-shrink-0">  
                    <button onclick="tlConfirmDelete(${JSON.stringify(allKeys).replace(/"/g, '&quot;')}, true)"  
                      class="text-choc-400 hover:text-choc-900 transition p-1">  
                      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">  
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5"  
                          d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/>  
                      </svg>  
                    </button>  
                  </div>  
                </div>`;  
        }).join('')}  
          </div>`;  
      });  
  
      // -- Singles --  
      const cartIsDE = tlLang === 'de';  
  
      const cartMoney = new Intl.NumberFormat(  
        cartIsDE ? 'de-CH' : 'fr-CH',  
        {  
          style: 'currency',  
          currency: cart.currency || 'CHF'  
        }  
      );  
  
      const formatCartMoney = cents => cartMoney.format(cents / 100);  
  
      const escapeCartHtml = value => String(value ?? '').replace(  
        /[&<>"']/g,  
        char => ({  
          '&': '&amp;',  
          '<': '&lt;',  
          '>': '&gt;',  
          '"': '&quot;',  
          "'": '&#39;'  
        }[char])  
      );  
  
      singles.forEach(item => {  
        const key = escapeCartHtml(JSON.stringify(item.key));  
  
        const discounted =  
          item.original_line_price > item.final_line_price;  
  
        // Neste produto: opção 1 = tamanho, opção 2 = cor.  
        const productDataElement = document.getElementById(  
          'tl-progressive-product-data'  
        );  
  
        const progressiveProduct = productDataElement  
          ? JSON.parse(productDataElement.textContent)  
          : null;  
  
        const isEssentielle =  
          String(item.product_id) === String(progressiveProduct?.id);  
  
        const options = item.variant_options || [];  
        const size = options[0] || '';  
        const rawColor = options[1] || '';  
  
        const color = rawColor === 'Black'  
          ? (cartIsDE ? 'Schwarz' : 'Noir')  
          : rawColor;  
  
        const variantLabel = isEssentielle  
          ? [color, size].filter(Boolean).join(' | ')  
          : item.variant_title || '';  
  
        bodyHtml += `  
          <div  
            class="flex items-start gap-4 mb-5 pb-5 border-b border-cream-200"  
          >  
            <img  
              src="${escapeCartHtml(tlCartImage(item))}"  
              alt="${escapeCartHtml(  
                [item.product_title, variantLabel].filter(Boolean).join(' — ')  
              )}"  
              style="  
                width:clamp(88px, 24vw, 112px);  
                aspect-ratio:4 / 5;  
                height:auto;  
                object-fit:cover;  
                flex-shrink:0;  
                align-self:flex-start;  
              "  
              class="rounded-sm border border-cream-300"  
            >  
  
            <div class="flex-1 min-w-0">  
              <p class="text-sm text-choc-900 font-medium leading-tight">  
                ${escapeCartHtml(item.product_title)}  
              </p>  
  
              <p class="text-xs text-choc-700/70 mt-1">  
                ${escapeCartHtml(variantLabel)}  
              </p>  
  
              <div class="mt-3 flex flex-wrap items-baseline gap-2">  
                <span class="text-sm font-semibold text-choc-900">  
                  ${formatCartMoney(item.final_line_price)}  
                </span>  
  
                ${discounted ? `  
                  <s class="text-xs text-choc-700/60">  
                    ${formatCartMoney(item.original_line_price)}  
                  </s>  
                ` : ''}  
              </div>  
  
              <div class="flex flex-wrap items-center gap-2 mt-3">  
                  <div class="tl-cart-quantity inline-flex items-center">  
  
                  <button  
                    type="button"  
                    data-tl-cart-control  
                    onclick="tlUpdateCart(${key}, ${item.quantity - 1})"  
                    ${item.quantity <= 1 ? 'disabled' : ''}  
                    aria-label="${  
                      cartIsDE ? 'Menge verringern' : 'Réduire la quantité'  
                    }"  
                    class="w-11 h-11 text-choc-900 disabled:opacity-40"  
                  >−</button>  
  
                  <span class="min-w-8 text-center text-sm text-choc-900">  
                    ${item.quantity}  
                  </span>  
  
                  <button  
                    type="button"  
                    data-tl-cart-control  
                    onclick="tlUpdateCart(${key}, ${item.quantity + 1})"  
                    aria-label="${  
                      cartIsDE ? 'Menge erhöhen' : 'Augmenter la quantité'  
                    }"  
                    class="w-11 h-11 text-choc-900"  
                  >+</button>  
                </div>  
  
                <button  
                  type="button"  
                  data-tl-cart-control  
                  onclick="tlConfirmDelete([${key}], false)"  
                  aria-label="${  
                    cartIsDE ? 'Artikel entfernen' : 'Supprimer cet article'  
                  }"  
                  class="w-11 h-11 inline-flex items-center justify-center text-choc-400 hover:text-choc-900 transition"  
                >  
                  <svg  
                    class="w-4 h-4"  
                    fill="none"  
                    stroke="currentColor"  
                    viewBox="0 0 24 24"  
                    aria-hidden="true"  
                  >  
                    <path  
                      stroke-linecap="round"  
                      stroke-linejoin="round"  
                      stroke-width="1.5"  
                      d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"  
                    />  
                  </svg>  
                </button>  
              </div>  
            </div>  
          </div>  
        `;  
      });  
  
  
      body.innerHTML = bodyHtml;  
  
  
      // ── DETAILS PAYMENT ────────────────────────────────────────────  
  
      const summaryProductData = document.getElementById(  
        'tl-progressive-product-data'  
      );  
  
      const summaryOfferData = document.getElementById(  
        'tl-progressive-offer-data'  
      );  
  
      const summaryProduct = summaryProductData  
        ? JSON.parse(summaryProductData.textContent)  
        : null;  
  
      const summaryOffer = summaryOfferData  
        ? JSON.parse(summaryOfferData.textContent)  
        : null;  
  
      const eligibleItems = summaryProduct  
        ? cart.items.filter(item =>  
            String(item.product_id) === String(summaryProduct.id)  
          )  
        : [];  
  
      const eligibleQuantity = eligibleItems.reduce(  
        (sum, item) => sum + item.quantity,  
        0  
      );  
  
      const summaryTiers = (summaryOffer?.tiers || [])  
        .filter(tier => tier.percentage > 0)  
        .slice()  
        .sort((a, b) => a.minimum - b.minimum);  
  
      // Só confirmar uma faixa quando todas as linhas elegíveis  
      // tiverem uma alocação real desse desconto.  
      const appliedTier = summaryOffer?.enabled  
        ? summaryTiers  
            .filter(tier =>  
              eligibleQuantity >= tier.minimum &&  
              eligibleItems.length > 0 &&  
              eligibleItems.every(item =>  
                (item.line_level_discount_allocations || []).some(  
                  allocation =>  
                    Number(allocation.amount) > 0 &&  
                    allocation.discount_application?.title ===  
                      tier.discountTitle  
                )  
              )  
            )  
            .pop()  
        : null;  
  
      let discountTag = '';  
  
      if (appliedTier) {  
        const bestTier = summaryTiers[summaryTiers.length - 1];  
        const isLastTier = appliedTier.minimum === bestTier.minimum;  
  
        const quantityLabel = cartIsDE  
          ? `${appliedTier.minimum} Stück${isLastTier ? ' +' : ''}`  
          : `${appliedTier.minimum} pièces${isLastTier ? ' +' : ''}`;  
  
      discountTag = `  
        <div  
          style="  
            display:flex;  
            align-items:center;  
            gap:10px;  
            width:100%;  
            box-sizing:border-box;  
            padding:9px 12px;  
            margin-bottom:12px;  
            border:1px dashed #8C8178;  
            border-radius:10px;  
            background:rgb(255 244 230);  
            position:relative;  
            margin-top:12px;  
  
          "  
        >  
          <img  
            src="{{ 'trimelle-discount-icon-20261016.png' | file_url }}"  
            alt=""  
            aria-hidden="true"  
            style="  
              width:23px;  
              height:23px;  
              object-fit:contain;  
              flex-shrink:0;  
            "  
          >  
  
          <span  
            style="  
              min-width:0;  
              font-size:12px;  
              line-height:1.4;  
              color:#231610;  
            "  
          >  
            Pack ${escapeCartHtml(quantityLabel)}  
            | Save ${Number(appliedTier.percentage)}%  
          </span>  
  
          <span  
            style="  
              margin-left:auto;  
              flex-shrink:0;  
              font-size:11px;  
              line-height:1.4;  
              font-weight:600;  
              color:#8C7868;  
            "  
          >  
            ${cartIsDE ? 'Angewendet' : 'Appliqué'}  
          </span>  
        </div>  
      `;  
  
      } else if (  
        summaryOffer?.enabled &&  
        eligibleQuantity === 1 &&  
        summaryTiers.length  
      ) {  
        const firstTier = summaryTiers[0];  
  
        discountTag = `  
          <div  
            class="rounded-lg border border-cream-300 px-3 py-2 mb-3"  
          >  
            <span class="text-xs text-choc-700">  
              Pack ·  
              ${cartIsDE  
                ? `Ab ${firstTier.minimum} Stück`  
                : `Dès ${firstTier.minimum} pièces`}  
              | SAVE ${Number(firstTier.percentage)}%  
            </span>  
          </div>  
        `;  
      }  
  
      const breakdownHtml = `  
        <p class="text-xs uppercase tracking-widest text-choc-700/50 font-medium mb-3">  
          ${escapeCartHtml(tlT.detailsPayment)}  
        </p>  
  
        ${discountTag}  
      `;  
  
      if (breakdown) breakdown.innerHTML = breakdownHtml;  
  
  
      // Economia total efetivamente refletida no subtotal.  
      // Não inclui comparação com compare_at_price.  
      const totalSavings = Math.max(  
        0,  
        cart.original_total_price - cart.total_price  
      );  
  
      const reductionEl = document.getElementById('tl-cart-reduction');  
      const reductionAmount = document.getElementById(  
        'tl-cart-reduction-amount'  
      );  
  
      if (reductionEl && reductionAmount) {  
        reductionAmount.textContent =  
          '−' + formatCartMoney(totalSavings);  
  
        reductionEl.classList.toggle('hidden', totalSavings === 0);  
      }  
  
      // Subtotal real retornado pelo carrinho.  
      total.textContent = formatCartMoney(cart.total_price);  
  
  
    } catch (e) {  
      console.error('tlLoadCart error:', e);  
    }  
  }  
  
  // ─── MODAL: Confirmação de delete ────────────────────────────────────────  
  function tlConfirmDelete(keys, isBundle) {  
    const modal = document.getElementById('tl-delete-modal');  
    const msg = document.getElementById('tl-delete-modal-msg');  
    const btn = document.getElementById('tl-delete-modal-confirm');  
  
    // -- Textos traduzidos --  
    msg.innerText = isBundle ? tlT.deleteBundle : tlT.deleteSingle;  
    document.getElementById('tl-delete-modal-no').innerText = tlT.confirmNo;  
    btn.innerText = tlT.confirmYes;  
  
    // -- Clonar DEPOIS de definir textos --  
    const newBtn = btn.cloneNode(true);  
    btn.parentNode.replaceChild(newBtn, btn);  
  
    newBtn.addEventListener('click', () => {  
      tlCloseDeleteModal();  
      if (isBundle) {  
        tlRemoveBundle(keys);  
      } else {  
        tlUpdateCart(keys[0], 0);  
      }  
    });  
  
    modal.classList.remove('pointer-events-none', 'opacity-0');  
    modal.classList.add('pointer-events-auto', 'opacity-100');  
  }  
  
  function tlCloseDeleteModal() {  
    const modal = document.getElementById('tl-delete-modal');  
    modal.classList.add('pointer-events-none', 'opacity-0');  
    modal.classList.remove('pointer-events-auto', 'opacity-100');  
  }  
  
  
  // ─── CART: tlUpdateCart / tlRemoveBundle ─────────────────────────────────  
  
  let tlCartUpdating = false;  
  
  async function tlUpdateCart(key, quantity) {  
    if (tlCartUpdating || tlAdding) return;  
  
    if (!Number.isSafeInteger(quantity) || quantity < 0) return;  
  
    tlCartUpdating = true;  
  
    const body = document.getElementById('tl-cart-body');  
    const addButton = document.getElementById('tl-progressive-add');  
  
    if (body) {  
      body.setAttribute('aria-busy', 'true');  
  
      body.querySelectorAll('[data-tl-cart-control]').forEach(button => {  
        button.disabled = true;  
      });  
    }  
  
    if (addButton) addButton.disabled = true;  
  
    const routeRoot = window.Shopify?.routes?.root || '/';  
  
    try {  
      const response = await fetch(`${routeRoot}cart/change.js`, {  
        method: 'POST',  
        headers: {  
          'Content-Type': 'application/json',  
          'Accept': 'application/json'  
        },  
        body: JSON.stringify({ id: key, quantity })  
      });  
  
      const result = await response.json();  
  
      if (!response.ok) {  
        throw new Error(  
          typeof result.description === 'string'  
            ? result.description  
            : typeof result.message === 'string'  
              ? result.message  
              : tlT.addError  
        );  
      }  
    } catch (error) {  
      tlShowToast(  
        error instanceof TypeError  
          ? (tlLang === 'de'  
            ? 'Verbindung unterbrochen. Prüfen Sie den Warenkorb.'  
            : 'Connexion interrompue. Vérifiez le panier.')  
          : error.message  
      );  
    } finally {  
      await tlLoadCart();  
  
      tlCartUpdating = false;  
  
      if (body) body.removeAttribute('aria-busy');  
  
      const selection =  
        window.tlProgressiveSelection?.getSelection();  
  
      if (addButton) {  
        addButton.disabled =  
          tlAdding || !selection?.variant?.available;  
      }  
  
      document.dispatchEvent(new CustomEvent('tl:cart-updated'));  
    }  
  }  
  
  
  async function tlRemoveBundle(keys) {  
    const updates = {};  
    keys.forEach(k => updates[k] = 0);  
    await fetch('/cart/update.js', {  
      method: 'POST',  
      headers: { 'Content-Type': 'application/json' },  
      body: JSON.stringify({ updates })  
    });  
      
    tlLoadCart();  
  }  
  
  // ─── UI ──────────────────────────────────────────────────────────────────  
  
  function toggleCart(open) {  
    const drawer = document.getElementById('tl-cart-drawer');  
    const panel = document.getElementById('tl-cart-panel');  
    if (open) {  
      drawer.style.opacity = '1';  
      drawer.style.pointerEvents = 'all';  
      drawer.style.zIndex = '99999';  
      panel.style.transform = 'translateX(0)';  
      tlLoadCart();  
    } else {  
      drawer.style.opacity = '0';  
      panel.style.transform = 'translateX(100%)';  
      setTimeout(() => { drawer.style.pointerEvents = 'none'; }, 300);  
    }  
  }  
  
  function openSizeModal() {  
    const m = document.getElementById('tl-size-modal');  
    m.style.opacity = '1';  
    m.style.pointerEvents = 'all';  
    m.style.zIndex = '99999';  
  }  
  
  function closeSizeModal() {  
    const m = document.getElementById('tl-size-modal');  
    m.style.opacity = '0';  
    m.style.pointerEvents = 'none';  
  }  
  
  function openStoryModal() {  
    const m = document.getElementById('tl-story-modal');  
    m.style.opacity = '1';  
    m.style.pointerEvents = 'all';  
    m.style.zIndex = '99999';  
  }  
  
  function closeStoryModal() {  
    const m = document.getElementById('tl-story-modal');  
    m.style.opacity = '0';  
    m.style.pointerEvents = 'none';  
  }  
  
  function tlToggleFaq(btn) {  
    const content = btn.nextElementSibling;  
    const icon = btn.querySelector('span:last-child');  
    content.classList.toggle('hidden');  
    icon.innerText = content.classList.contains('hidden') ? '+' : '−';  
  }  
  
  function tlShowToast(msg) {  
    const toast = document.getElementById('tl-toast');  
    document.getElementById('tl-toast-msg').innerText = msg;  
    toast.classList.remove('translate-y-20', 'opacity-0');  
    toast.classList.add('translate-y-0', 'opacity-100');  
    setTimeout(() => {  
      toast.classList.remove('translate-y-0', 'opacity-100');  
      toast.classList.add('translate-y-20', 'opacity-0');  
    }, 3500);  
  }  
  
  // ─── INIT ─────────────────────────────────────────────────────────────────  
  
  document.addEventListener('DOMContentLoaded', function () {  
    if (document.getElementById('tl-bundle-section')) {  
      tlSelectBundle(2);  
    }  
  
    tlLoadCart();  
  });  
  
  // ─── POLICY SLIDE-OVER ───────────────────────────────────────────────────  
  
  function tlOpenPolicy(e, url, title) {  
    e.preventDefault();  
    var overlay = document.getElementById('tl-policy-overlay');  
    var drawer = document.getElementById('tl-policy-drawer');  
    var content = document.getElementById('tl-policy-content');  
    document.getElementById('tl-policy-title').textContent = title || '';  
    content.innerHTML = '<p class="text-center py-12 text-choc-400 text-xs tracking-widest uppercase">Chargement...</p>';  
    overlay.classList.remove('opacity-0', 'pointer-events-none');  
    overlay.classList.add('opacity-100');  
    drawer.style.transform = 'translateX(0)';  
    document.body.style.overflow = 'hidden';  
    fetch(url)  
      .then(function (r) { return r.text(); })  
      .then(function (html) {  
        var doc = new DOMParser().parseFromString(html, 'text/html');  
        var el = doc.querySelector('.main-page') ||  
          doc.querySelector('[class*="policy"]') ||  
          doc.querySelector('.shopify-policy__body') ||  
          doc.querySelector('.rte') ||  
          doc.querySelector('article') ||  
          doc.querySelector('main');  
        content.innerHTML = el ? el.innerHTML : '<p class="text-choc-400 text-center py-8">Contenu non disponible.</p>';  
      });  
  }  
  
  function tlClosePolicy() {  
    var overlay = document.getElementById('tl-policy-overlay');  
    var drawer = document.getElementById('tl-policy-drawer');  
    overlay.classList.add('opacity-0', 'pointer-events-none');  
    overlay.classList.remove('opacity-100');  
    drawer.style.transform = 'translateX(-100%)';  
    document.body.style.overflow = '';  
  }  
  
  document.addEventListener('keydown', function (e) { if (e.key === 'Escape') tlClosePolicy(); });  
  
  // ─── MENU SLIDE-OVER ─────────────────────────────────────────────────────  
  
  function tlOpenMenu() {  
    var overlay = document.getElementById('tl-menu-overlay');  
    var drawer = document.getElementById('tl-mobile-menu');  
    overlay.classList.remove('opacity-0', 'pointer-events-none');  
    overlay.classList.add('opacity-100');  
    drawer.style.transform = 'translateX(0)';  
    document.body.style.overflow = 'hidden';  
  }  
  
  function tlCloseMenu() {  
    var overlay = document.getElementById('tl-menu-overlay');  
    var drawer = document.getElementById('tl-mobile-menu');  
    overlay.classList.add('opacity-0', 'pointer-events-none');  
    overlay.classList.remove('opacity-100');  
    drawer.style.transform = 'translateX(-100%)';  
    document.body.style.overflow = '';  
  }  
  
  // ─── SCROLL HEADER ───────────────────────────────────────────────────────  
  
  window.addEventListener('scroll', function () {  
    var topbarHeight = 33;  
    var offset = Math.min(window.scrollY, topbarHeight);  
    document.querySelector('header').style.top = (topbarHeight - offset) + 'px';  
  });  
  
  async function tlOpenCart() {  
    const res = await fetch('/cart.js');  
    const cart = await res.json();  
    if (cart.item_count === 0) {  
      document.getElementById('tl-bundle-section')?.scrollIntoView({ behavior: 'smooth', block: 'start' });  
      return;  
    }  
    toggleCart(true);  
  }  
  
  (() => {  
  const key = 'trimelleLocaleScrollY';  
  const savedY = sessionStorage.getItem(key);  
  
  if (savedY === null) return;  
  
  sessionStorage.removeItem(key);  
  
  const restoreScroll = () => {  
    requestAnimationFrame(() => window.scrollTo(0, Number(savedY)));  
  };  
  
  if (document.readyState === 'complete') {  
    restoreScroll();  
  } else {  
    window.addEventListener('load', restoreScroll, { once: true });  
  }  
})();  
  
(function () {  
  const info = document.querySelector('#produit .tl-product-info');  
  if (!info) return;  
  
  const media = window.matchMedia('(min-width: 768px)');  
  let frame = null;  
  
  function updateSticky() {  
    frame = null;  
  
    if (!media.matches) {  
      info.style.removeProperty('--tl-info-sticky-top');  
      return;  
    }  
  
    const topSpace = 80;  
    const bottomSpace = 24;  
    const infoHeight = info.getBoundingClientRect().height;  
  
    const stickyTop = Math.min(  
      topSpace,  
      window.innerHeight - infoHeight - bottomSpace  
    );  
  
    info.style.setProperty(  
      '--tl-info-sticky-top',  
      `${stickyTop}px`  
    );  
  }  
  
  function scheduleUpdate() {  
    if (frame !== null) cancelAnimationFrame(frame);  
    frame = requestAnimationFrame(updateSticky);  
  }  
  
  const observer = new ResizeObserver(scheduleUpdate);  
  observer.observe(info);  
  
  window.addEventListener('resize', scheduleUpdate);  
  media.addEventListener('change', scheduleUpdate);  
  
  if (document.fonts && document.fonts.ready) {  
    document.fonts.ready.then(scheduleUpdate);  
  }  
  
  scheduleUpdate();  
})();  
  
// ─── PROGRESSIVE: SELEÇÃO E PREÇOS ───────────────────────────────  
  
(function () {  
  function initProgressiveSelection() {  
    const root = document.getElementById('tl-progressive-section');  
    const dataElement = document.getElementById(  
      'tl-progressive-product-data'  
    );  
  
    if (!root || !dataElement || root.dataset.selectionReady) return;  
  
    const product = JSON.parse(dataElement.textContent);  
    const variants = product.variants;  
  
    if (!Array.isArray(variants) || !variants.length) return;  
  
    // Usar uma referência interna estável para a cor,  
    // independentemente do idioma recebido.  
    variants.forEach(variant => {  
      const color = String(variant.option2 || '')  
        .trim()  
        .toLowerCase();  
  
      if (['black', 'schwarz', 'noir'].includes(color)) {  
        variant.option2 = 'Black';  
      } else if (color === 'beige') {  
        variant.option2 = 'Beige';  
      }  
    });  
  
    root.dataset.selectionReady = 'true';  
  
  
    const isDE = tlLang === 'de';  
  
    const elements = {  
      colors: document.getElementById('tl-progressive-colors'),  
      sizes: document.getElementById('tl-progressive-sizes'),  
      colorLabel: document.getElementById('tl-progressive-color-label'),  
      sizeLabel: document.getElementById('tl-progressive-size-label'),  
      quantity: document.getElementById('tl-progressive-quantity'),  
      minus: document.getElementById('tl-progressive-minus'),  
      plus: document.getElementById('tl-progressive-plus'),  
      subtotal: document.getElementById(  
        'tl-progressive-selection-subtotal'  
      ),  
      unitPrice: document.getElementById('tl-progressive-unit-price'),  
      error: document.getElementById('tl-progressive-error')  
    };  
  
    const colors = [...new Set(variants.map(v => v.option2))];  
    const sizes = [...new Set(variants.map(v => v.option1))]  
      .filter(size => size !== 'XXL');  
  
    const initial =  
      variants.find(v => v.option2 === 'Beige' && v.available) ||  
      variants.find(v => v.available) ||  
      variants[0];  
  
    const state = {  
      color: initial.option2,  
      size: initial.option1,  
      quantity: 1  
    };  
  
    const moneyFormatter = new Intl.NumberFormat(  
      isDE ? 'de-CH' : 'fr-CH',  
      {  
        style: 'currency',  
        currency: product.currency || 'CHF'  
      }  
    );  
  
    function money(cents) {  
      return moneyFormatter.format(cents / 100);  
    }  
  
    function colorLabel(color) {  
      if (color === 'Black') return isDE ? 'Schwarz' : 'Noir';  
      return color;  
    }  
  
    function selectedVariant() {  
      return variants.find(v =>  
        v.option1 === state.size &&  
        v.option2 === state.color  
      );  
    }  
  
    function normalizeQuantity(value) {  
      const number = Number(value);  
  
      if (!Number.isFinite(number)) return 1;  
  
      return Math.max(  
        1,  
        Math.min(Number.MAX_SAFE_INTEGER, Math.floor(number))  
      );  
    }  
  
    function render() {  
      const variant = selectedVariant();  
      const available = Boolean(variant && variant.available);  
  
      elements.colorLabel.textContent = colorLabel(state.color);  
      elements.sizeLabel.textContent = state.size;  
      elements.quantity.value = state.quantity;  
  
      elements.quantity.disabled = !available;  
      elements.plus.disabled = !available;  
      elements.minus.disabled = !available || state.quantity <= 1;  
  
      elements.error.classList.toggle('hidden', available);  
      elements.error.textContent = available  
        ? ''  
        : (isDE  
          ? 'Diese Kombination ist nicht verfügbar.'  
          : 'Cette combinaison est indisponible.');  
  
        if (variant) {  
          const offerData = document.getElementById(  
            'tl-progressive-offer-data'  
          );  
  
          const offer = offerData  
            ? JSON.parse(offerData.textContent)  
            : null;  
  
          const cart = window.tlProgressiveCart;  
          const cartKnown = cart && Array.isArray(cart.items);  
  
          const quantityInCart = cartKnown  
            ? cart.items  
                .filter(item =>  
                  String(item.product_id) === String(product.id)  
                )  
                .reduce((sum, item) => sum + item.quantity, 0)  
            : 0;  
  
          const totalAfterAdd = quantityInCart + state.quantity;  
  
          const tier =  
            offer?.enabled && cartKnown && available  
              ? offer.tiers  
                  .slice()  
                  .sort((a, b) => a.minimum - b.minimum)  
                  .filter(t => totalAfterAdd >= t.minimum)  
                  .pop()  
              : null;  
  
          const percentage = tier?.percentage || 0;  
          const subtotalBeforeDiscount = variant.price * state.quantity;  
  
          if (percentage > 0) {  
            const estimatedSubtotal = Math.round(  
              subtotalBeforeDiscount * (1 - percentage / 100)  
            );  
  
            elements.subtotal.textContent =  
              money(estimatedSubtotal);  
  
            const desktopPriceNote = isDE  
              ? `Nach dem Hinzufügen: geschätzte Summe dieser Auswahl mit ${percentage}% Rabatt.`  
              : `Après ajout : sous-total estimé de cette sélection avec ${percentage}% de remise.`;  
  
            elements.unitPrice.innerHTML = `  
              <span class="tl-price-note-mobile">${percentage}%</span>  
              <span class="tl-price-note-desktop">${desktopPriceNote}</span>  
            `;  
  
          } else {  
            elements.subtotal.textContent =  
              money(subtotalBeforeDiscount);  
  
            elements.unitPrice.textContent =  
              money(variant.price) +  
              (isDE  
                ? ' / Stück, vor Rabatt'  
                : ' / pièce, avant remise');  
          }  
        } else {  
          elements.subtotal.textContent = '—';  
          elements.unitPrice.textContent = '';  
        }  
  
  
        elements.colors.querySelectorAll('button').forEach(button => {  
          const selected = button.dataset.color === state.color;  
  
          button.setAttribute('aria-pressed', String(selected));  
          button.style.removeProperty('border-color');  
  
          const swatch = button.querySelector('span[aria-hidden="true"]');  
  
          if (swatch) {  
            swatch.style.outline = selected  
              ? '1px solid #231610'  
              : 'none';  
  
            swatch.style.outlineOffset = '3px';  
          }  
        });  
  
  
      elements.sizes.querySelectorAll('button').forEach(button => {  
        const size = button.dataset.size;  
  
        const matchingVariant = variants.find(v =>  
          v.option1 === size &&  
          v.option2 === state.color  
        );  
  
        const selected = size === state.size;  
        const unavailable =  
          !matchingVariant || !matchingVariant.available;  
  
        button.disabled = unavailable;  
        button.setAttribute('aria-pressed', String(selected));  
        button.style.opacity = unavailable ? '0.4' : '1';  
        button.style.textDecoration = unavailable  
          ? 'line-through'  
          : 'none';  
        button.style.borderColor = selected  
          ? '#231610'  
          : '#E6DCCF';  
        button.style.background = selected  
          ? '#F2ECE4'  
          : 'transparent';  
      });  
  
      tlRenderAddButton();  
      root.dispatchEvent(new CustomEvent('tl:selection-change', {  
        bubbles: true,  
        detail: {  
          productId: product.id,  
          variant: variant || null,  
          color: state.color,  
          size: state.size,  
          quantity: state.quantity,  
          available  
        }  
      }));  
    }  
  
    colors  
      .slice()  
      .sort((a, b) => {  
        if (a === b) return 0;  
        if (a === 'Beige') return -1;  
        if (b === 'Beige') return 1;  
        return 0;  
      })  
      .forEach(color => {  
        const button = document.createElement('button');  
        button.type = 'button';  
        button.dataset.color = color;  
        button.className =  
        'flex flex-col items-center gap-2 p-2 text-xs text-choc-900';  
  
        const swatch = document.createElement('span');  
        swatch.className =  
          'block w-9 h-9 rounded-full border border-cream-300';  
        swatch.style.background =  
          color === 'Black' ? '#1A1A1A' : '#E3CBB5';  
        swatch.setAttribute('aria-hidden', 'true');  
  
        const label = document.createElement('span');  
        label.textContent = colorLabel(color);  
  
        button.append(swatch, label);  
  
        button.addEventListener('click', () => {  
          state.color = color;  
          render();  
        });  
  
        elements.colors.appendChild(button);  
      });  
  
    sizes.forEach(size => {  
      const button = document.createElement('button');  
      button.type = 'button';  
      button.dataset.size = size;  
      button.textContent = size;  
      button.className =  
        'min-w-11 h-11 px-3 rounded-lg border text-sm ' +  
        'text-choc-900 disabled:cursor-not-allowed';  
  
      button.addEventListener('click', () => {  
        state.size = size;  
        render();  
      });  
  
      elements.sizes.appendChild(button);  
    });  
  
    elements.minus.addEventListener('click', () => {  
      state.quantity = normalizeQuantity(state.quantity - 1);  
      render();  
    });  
  
    elements.plus.addEventListener('click', () => {  
      state.quantity = normalizeQuantity(state.quantity + 1);  
      render();  
    });  
  
    elements.quantity.addEventListener('change', () => {  
      state.quantity = normalizeQuantity(elements.quantity.value);  
      render();  
    });  
  
    window.tlProgressiveSelection = {  
      getSelection() {  
        return {  
          productId: product.id,  
          variant: selectedVariant() || null,  
          color: state.color,  
          size: state.size,  
          quantity: state.quantity  
        };  
      }  
    };  
  
    document.addEventListener('tl:cart-data', render);  
  
    render();  
  }  
  
  if (document.readyState === 'loading') {  
    document.addEventListener(  
      'DOMContentLoaded',  
      initProgressiveSelection,  
      { once: true }  
    );  
  } else {  
    initProgressiveSelection();  
  }  
})();  
  
  
// ─── PROGRESSIVE: GALERIA POR COR ────────────────────────────────  
  
(function () {  
  let currentColor = null;  
  
  function filterGallery(color, force = false) {  
    const carousel = document.getElementById('tl-carousel');  
    if (!carousel || (!force && currentColor === color)) return;  
  
    currentColor = color;  
  
    const slides = Array.from(carousel.children);  
    const visibleSlides = [];  
  
    slides.forEach(slide => {  
      const matches = slide.dataset.galleryColor === color;  
      slide.hidden = !matches;  
  
      if (matches) {  
        slide.style.removeProperty('display');  
        visibleSlides.push(slide);  
      } else {  
        slide.style.setProperty('display', 'none', 'important');  
      }  
    });  
  
    const total = visibleSlides.length;  
  
    carousel.style.width = `${Math.max(1, total) * 100}%`;  
  
    visibleSlides.forEach(slide => {  
      slide.style.width = `${100 / total}%`;  
    });  
  
    let emptyMessage = document.getElementById(  
      'tl-gallery-empty-message'  
    );  
  
    if (!emptyMessage) {  
      emptyMessage = document.createElement('p');  
      emptyMessage.id = 'tl-gallery-empty-message';  
      emptyMessage.className =  
        'absolute inset-0 flex items-center justify-center ' +  
        'p-6 text-center text-sm text-choc-700';  
      emptyMessage.setAttribute('role', 'status');  
      carousel.parentElement.appendChild(emptyMessage);  
    }  
  
    emptyMessage.textContent = tlLang === 'de'  
      ? 'Für diese Farbe sind noch keine Fotos verfügbar.'  
      : 'Les photos de cette couleur ne sont pas encore disponibles.';  
  
    emptyMessage.style.display = total ? 'none' : 'flex';  
  
    const wrapper = carousel.parentElement;  
  
    if (!total) {  
      wrapper.style.minHeight = '240px';  
    } else {  
      wrapper.style.removeProperty('min-height');  
    }  
  
    const dots = document.getElementById('tl-carousel-dots');  
  
    if (dots) {  
      dots.replaceChildren();  
  
      visibleSlides.forEach((slide, index) => {  
        const dot = document.createElement('button');  
        dot.type = 'button';  
        dot.id = `tl-dot-${index}`;  
        dot.setAttribute('aria-label', `Photo ${index + 1}`);  
        dot.addEventListener('click', () => tlCarousel(index));  
        dots.appendChild(dot);  
      });  
    }  
  
    tlCarouselIndex = 0;  
    carousel.style.transform = 'translateX(0)';  
    requestAnimationFrame(() => tlCarousel(0));  
  }  
  
  document.addEventListener('tl:selection-change', event => {  
    filterGallery(event.detail.color);  
  });  
  
  function initializeGallery() {  
    const selection =  
      window.tlProgressiveSelection?.getSelection();  
  
    if (selection) filterGallery(selection.color, true);  
  }  
  
  if (document.readyState === 'loading') {  
    document.addEventListener(  
      'DOMContentLoaded',  
      initializeGallery,  
      { once: true }  
    );  
  } else {  
    initializeGallery();  
  }  
})();  
  
  
// ─── PROGRESSIVE: BOTÃO DE COMPRA ───────────────────────────────  
  
(function () {  
  function initializeProgressiveAdd() {  
    const button = document.getElementById('tl-progressive-add');  
    if (!button || button.dataset.addReady) return;  
  
    button.dataset.addReady = 'true';  
    button.addEventListener('click', tlAddToCart);  
  
    function updateButton() {  
      const selection =  
        window.tlProgressiveSelection?.getSelection();  
  
      button.disabled =  
        tlAdding || !selection?.variant?.available;  
    }  
  
    document.addEventListener(  
      'tl:selection-change',  
      updateButton  
    );  
  
    updateButton();  
  }  
  
  if (document.readyState === 'loading') {  
    document.addEventListener(  
      'DOMContentLoaded',  
      initializeProgressiveAdd,  
      { once: true }  
    );  
  } else {  
    initializeProgressiveAdd();  
  }  
})();  
  
// ─── PROGRESSIVE: CARTÕES E FAIXA REAL DO CARRINHO ───────────────  
  
(function () {  
  function initializeTierCards() {  
    const data = document.getElementById(  
      'tl-progressive-offer-data'  
    );  
    const productData = document.getElementById(  
      'tl-progressive-product-data'  
    );  
    const container = document.getElementById(  
      'tl-progressive-tier-cards'  
    );  
    const message = document.getElementById(  
      'tl-progressive-tier-message'  
    );  
  
    if (!data || !productData || !container || !message) return;  
  
    const offer = JSON.parse(data.textContent);  
    const product = JSON.parse(productData.textContent);  
    const isDE = tlLang === 'de';  
  
    const tiers = offer.tiers  
      .slice()  
      .sort((a, b) => a.minimum - b.minimum);  
  
    container.replaceChildren();  
  
    tiers.filter(tier => tier.percentage > 0).forEach(tier => {  
      const card = document.createElement('div');  
      card.dataset.tierMinimum = String(tier.minimum);  
      card.className =  
        'min-w-0 rounded-lg border border-cream-300 px-1 py-2 text-center';  
  
      const quantity = document.createElement('p');  
      quantity.className = 'text-xs text-choc-900 mt-1';  
  
      const last =  
        tier.minimum === tiers[tiers.length - 1].minimum;  
  
      quantity.textContent = isDE  
        ? `${tier.minimum} Stk.${last ? ' +' : ''}`  
        : `${tier.minimum} pcs.${last ? ' +' : ''}`;  
  
      const value = document.createElement('p');  
      value.className =  
        'text-lg font-semibold leading-tight text-choc-700';  
      value.textContent = `${tier.percentage}%`;  
  
      card.style.position = 'relative';  
      card.style.marginBottom = '10px';  
  
      card.append(value, quantity);  
  
      if (tier.minimum === 3 || last) {  
        const tag = document.createElement('span');  
  
        tag.textContent = tier.minimum === 3  
          ? (isDE ? 'CLEVER GEWÄHLT' : 'CHOIX MALIN')  
          : (isDE ? 'BESTER PREIS' : 'MEILLEUR PRIX');  
  
        Object.assign(tag.style, {  
          position: 'absolute',  
          bottom: '-10px',  
          left: '50%',  
          transform: 'translateX(-50%)',  
          whiteSpace: 'nowrap',  
          padding: '3px 6px',  
          border: '1px solid #D5C4B1',  
          borderRadius: '5px',  
          backgroundColor: '#F2ECE4',  
          color: '#231610',  
          fontSize: '7px',  
          fontWeight: '600',  
          lineHeight: '1.2',  
          letterSpacing: '0.2px'  
        });  
  
        card.appendChild(tag);  
      }  
  
      container.appendChild(card);  
  
    });  
  
    let currentCart = window.tlProgressiveCart;  
  
    let currentSelection =  
      window.tlProgressiveSelection?.getSelection() || null;  
  
    function renderTierStates() {  
      const cards = Array.from(  
        container.querySelectorAll('[data-tier-minimum]')  
      );  
  
      cards.forEach(card => {  
        card.style.removeProperty('border-color');  
        card.style.removeProperty('background-color');  
        card.style.removeProperty('border-style');  
        card.removeAttribute('aria-label');  
        card.removeAttribute('title');  
        delete card.dataset.tierState;  
      });  
  
      if (message) {  
        message.textContent = '';  
        message.removeAttribute('title');  
      }  
  
      if (!offer.enabled) return;  
  
      const cartKnown =  
        currentCart && Array.isArray(currentCart.items);  
  
      const eligible = cartKnown  
        ? currentCart.items.filter(item =>  
            String(item.product_id) === String(product.id)  
          )  
        : [];  
  
      const quantityInCart = eligible.reduce(  
        (sum, item) => sum + Number(item.quantity),  
        0  
      );  
  
      const discountTiers = tiers.filter(  
        tier => tier.percentage > 0  
      );  
  
      // Confirmar apenas descontos realmente atribuídos  
      // a todas as linhas elegíveis deste produto.  
      const actualTier = cartKnown  
        ? discountTiers  
            .filter(tier =>  
              quantityInCart >= tier.minimum &&  
              eligible.length > 0 &&  
              eligible.every(item =>  
                (item.line_level_discount_allocations || []).some(  
                  allocation =>  
                    Number(allocation.amount) > 0 &&  
                    allocation.discount_application?.title ===  
                      tier.discountTitle  
                )  
              )  
            )  
            .pop()  
        : null;  
  
      function highlightTier(tier, preview) {  
        const card = cards.find(element =>  
          Number(element.dataset.tierMinimum) === tier.minimum  
        );  
  
        if (!card) return;  
  
        const label = preview  
          ? (isDE  
            ? `Nach dem Hinzufügen: voraussichtlich ${tier.percentage}% Rabatt`  
            : `Après ajout : remise prévue de ${tier.percentage}%`)  
          : (isDE  
            ? `${tier.percentage}% Rabatt im Warenkorb angewendet`  
            : `Remise de ${tier.percentage}% appliquée dans le panier`);  
  
        card.style.borderColor = '#8C6858';  
        card.style.backgroundColor = preview  
          ? 'transparent'  
          : '#F2ECE4';  
        card.style.borderStyle = preview ? 'dashed' : 'solid';  
  
        card.dataset.tierState = preview ? 'preview' : 'applied';  
        card.setAttribute('aria-label', label);  
        card.setAttribute('title', label);  
      }  
  
      // O estado confirmado nunca é apagado pela previsão.  
      if (actualTier) {  
        highlightTier(actualTier, false);  
      }  
  
      // Sem dados do carrinho, não assumir que está vazio.  
      if (!cartKnown) return;  
  
      const selection = currentSelection;  
      const selectedQuantity = Number(selection?.quantity);  
  
      const validSelection =  
        selection &&  
        String(selection.productId) === String(product.id) &&  
        Boolean(selection.variant?.available) &&  
        selection.available !== false &&  
        Number.isSafeInteger(selectedQuantity) &&  
        selectedQuantity > 0;  
  
      if (!validSelection) return;  
  
      const quantityAfterAdd =  
        quantityInCart + selectedQuantity;  
  
      const predictedTier = discountTiers  
        .filter(tier => quantityAfterAdd >= tier.minimum)  
        .pop();  
  
      // Sem faixa confirmada: permitir a previsão.  
      // Com faixa confirmada: prever apenas uma faixa superior.  
      if (  
        predictedTier &&  
        (!actualTier ||  
          predictedTier.percentage > actualTier.percentage)  
      ) {  
        highlightTier(predictedTier, true);  
      }  
    }  
  
    document.addEventListener('tl:cart-data', event => {  
      currentCart = event.detail;  
  
      const latestSelection =  
        window.tlProgressiveSelection?.getSelection();  
  
      if (latestSelection) {  
        currentSelection = latestSelection;  
      }  
  
      renderTierStates();  
    });  
  
    document.addEventListener('tl:selection-change', event => {  
      currentSelection = event.detail;  
      renderTierStates();  
    });  
  
    renderTierStates();  
  
  }  
  
  if (document.readyState === 'loading') {  
    document.addEventListener(  
      'DOMContentLoaded',  
      initializeTierCards,  
      { once: true }  
    );  
  } else {  
    initializeTierCards();  
  }  
})();  
  
  
</script>  
