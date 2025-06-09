# 🧩 Estrutura de Código para Páginas ClickBank

Este documento descreve os elementos obrigatórios e scripts que devem estar presentes nas páginas de vendas e upsells integradas com ClickBank.

---

## 📌 Footer Obrigatório

Inserir o seguinte código no **rodapé das páginas**:

```html
<nav>
  <a href="terms.html">Terms & Conditions</a>
  <a href="privacy.html">Privacy Policy</a>
  <a href="contact.html">Contact</a>
  <a href="Disclaimer.html">Disclaimer</a>
  <a href="reference.html">Reference</a>
  <a href="refund.html">Refund</a>
  <a href="shipping.html">Shipping</a>
</nav>

<style>
  .area-contato {
    display: grid;
    gap: 8px;
    text-align: center;
    padding-bottom: 24px;
  }
  .area-contato a {
    color: rgb(37, 26, 192) !important;
  }
</style>

<div class="area-contato">
  <span>
    For Product Support, please contact the vendor
    <a href="mailto:support@arialief.com">HERE</a>.
  </span>
  <span>
    For Order Support, please contact ClickBank
    <a href="https://www.clkbank.com/">HERE</a>.
  </span>
</div>

<div class="disclaimer">
  <p style="margin-bottom: 14px">
    ClickBank is the retailer of this product. CLICKBANK® is a
    registered trademark of Click Sales, Inc., a Delaware corporation
    located at 1444 S. Entertainment Ave., Suite 410 Boise, ID 83709,
    USA and used by permission. ClickBank’s role as retailer does not
    constitute an endorsement, approval or review of this product or
    any claim, statement or opinion used in promotion of this product.
  </p>
  <p>
    Testimonials, case studies, and examples found on this page are
    results that have been forwarded to us by users of Arialief and
    related products are not intended to represent or guarantee that
    anyone will achieve the same or similar results.
  </p>
</div>

<div class="disclaimer" style="background: #044356">
  <p style="margin-bottom: 0; font-size: 14px; color: #fff">
    Statements on this website have not been evaluated by the Food and
    Drug Administration. Products are not intended to diagnose, treat,
    cure or prevent any disease. If you are pregnant, nursing, taking
    medication, or have a medical condition, consult your physician
    before using our products.
  </p>
</div>
📦 Parâmetros de URL e LocalStorage
Todas as páginas de venda devem conter este script para capturar parâmetros da URL e armazená-los no localStorage:

 
<script>
  document.addEventListener("DOMContentLoaded", function () {
    const keys = {
      param: "param_vid",
      vtid: "param_vtid",
      rtkcid: "param_rtkcid",
      rtkcmpid: "param_rtkcmpid",
      affiliate: "param_affiliate",
      tid: "param_tid",
    };

    const urlParams = new URLSearchParams(window.location.search);

    const values = {
      param: urlParams.get("param") || localStorage.getItem(keys.param) || "vid",
      vtid: urlParams.get("vtid") || localStorage.getItem(keys.vtid) || "",
      rtkcid: urlParams.get("rtkcid") || localStorage.getItem(keys.rtkcid) || "",
      rtkcmpid: urlParams.get("rtkcmpid") || localStorage.getItem(keys.rtkcmpid) || "",
      affiliate: urlParams.get("affiliate") || localStorage.getItem(keys.affiliate) || "",
      tid: urlParams.get("tid") || localStorage.getItem(keys.tid) || "",
    };

    for (const key in values) {
      if (values[key]) {
        localStorage.setItem(keys[key], values[key]);
      }
    }

    const links = document.querySelectorAll(".area-kits a");

    links.forEach((link) => {
      const url = new URL(link.href);
      if (values.param) url.searchParams.set("param", values.param);
      if (values.vtid) url.searchParams.set("vtid", values.vtid);
      if (values.rtkcid) url.searchParams.set("rtkcid", values.rtkcid);
      if (values.rtkcmpid) url.searchParams.set("rtkcmpid", values.rtkcmpid);
      if (values.affiliate) url.searchParams.set("affiliate", values.affiliate);
      if (values.tid) url.searchParams.set("tid", values.tid);

      link.href = url.toString();
    });
  });
</script>
Obs: Os parâmetros que precisam passar entre páginas devem obrigatoriamente estar incluídos no objeto keys.

🛒 Propagação dos Parâmetros no Link de Checkout
Inserir nas páginas de venda, upsell e downsell:


<script>
  function getQueryParams() {
    return new URLSearchParams(window.location.search);
  }

  function combineParams(originalHref, currentParams) {
    let [baseUrl, originalParamsString] = originalHref.split("?");
    let originalParams = new URLSearchParams(originalParamsString || "");

    currentParams.forEach((value, key) => {
      if (!originalParams.has(key)) {
        originalParams.append(key, value);
      }
    });

    let finalParamsString = originalParams.toString();
    return finalParamsString ? \`\${baseUrl}?\${finalParamsString}\` : baseUrl;
  }

  document.addEventListener("DOMContentLoaded", function () {
    let buttons = document.querySelectorAll(".area-kits a");
    let currentParams = getQueryParams();

    buttons.forEach(function (button) {
      let originalHref = button.getAttribute("href");
      button.setAttribute("href", combineParams(originalHref, currentParams));
    });
  });
</script>
🎬 Páginas com Vídeo (VSL, Upsell, WB)
Inserir o HTML e script a seguir para exibir conteúdo em vídeo ou texto, dependendo do parâmetro param na URL:

 
<div class="card sempa">
  <!-- Conteúdo em texto -->
</div>

<div class="compa center">
  <!-- Conteúdo em vídeo -->
</div>
 
<script>
  document.addEventListener("DOMContentLoaded", function () {
    const sempa = document.querySelector(".card.sempa");
    const compaWrapper = document.querySelector(".compa");
    const dataDelayElements = document.querySelectorAll("[data-delay]");
    const urlParams = new URLSearchParams(window.location.search);
    const param = urlParams.get("param");
    const vidKey = "param_vid";

    if (param === "vid") {
      if (sempa) sempa.style.display = "none";
      if (compaWrapper) compaWrapper.style.display = "flex";
    } else {
      if (sempa) sempa.style.display = "block";
      if (compaWrapper) compaWrapper.remove();
    }

    setTimeout(() => {
      if (sempa && window.getComputedStyle(sempa).display === "block") {
        dataDelayElements.forEach((el) => {
          el.style.display = "block";
        });
        localStorage.removeItem(vidKey);
        console.log("🧹 `param_vid` removido do localStorage.");
      }
    }, 0);
  });
</script>
