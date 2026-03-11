
# Taloustieteilijän portfolio 📈                            
## Analyyttinen ote talouteen, markkinoihin ja rakenteisiin

Hei! Olen Leena, ekonomisti. Lähestyn taloutta datan kautta: minua kiinnostaa, mitä luvut näyttävät ja miten niistä saa koottua selkeän kokonaisuuden. Teen analyysejä, koska haluan ymmärtää asioita paremmin.

Tämä portfolio kokoaa projekteja, joissa olen tarkastellut talouden ja rahoituksen kehitystä kvantitatiivisin menetelmin. Mukana on esimerkiksi aikasarja‑ ja regressiomalleja sekä talouden rakenteiden ja muutosten tarkastelua datan avulla. **Tavoitteena on päästä kiinni siihen, mikä on olennaista ja miten sen voi perustella.**
Olen valinnut portfoliooni aiheita, jotka kuvaavat minulle keskeisiä talouden ilmiöitä: julkisen talouden kestävyyttä, suhdanteiden vaikutusta velkaantumiseen, luottomarkkinoiden dynamiikkaa sekä rahoitusmarkkinoiden riskejä. Nämä teemat täydentävät toisiaan ja heijastavat kiinnostustani siihen, miten talouden rakenteet, markkinat ja makromuuttujat kytkeytyvät toisiinsa.

Analyysit on pääosin toteutettu R:llä. Visualisointiin olen hyödyntänyt Power BI:tä, R:ää sekä Exceliä. Osaamiseni kattaa myös monen muun tilasto-ohjelman käytön, mutta viihdyn eniten R:n parissa ja myös siksi, että se on kotikoneelle mahdollista saada.
**Olen myös halunnut oppia SQL:llän:** Rakensin oman palvelinympäristön, jonka alle toteutin talousdatan käsittelyyn tarvittavat taulut ja tietokantarakenteet. Keräsin talousdataa Tilastokeskuksen sivuilta, siirsin sen ensin Exceliin ja vein sen jälkeen SQL‑tietokantaan jatkokäsittelyä varten. Työstin ja analysoin dataa SQL‑kyselyillä (JOINit, aggregaatit, suodatukset), jotta pystyin tutkimaan ja muokkaamaan aineistoa tehokkaasti.


**Makrotalouden aiheet** 

[Korkotason rooli Suomen kestävyysvajeessa: kustannus, tulonlähde vai molemmat?](./kestävyysvaje_portfolio.pdf) 

[Bkt:n volyymin muutoksen, julkisyhteisöjen tulojen ja menojen sekä työttömyysasteen
    vaikutus EDP-velkaan](./EDP_portfolio.pdf)
    
[Korkotason, asuntojen hintojen sekä työttömyysasteen vaikutus uusien lainojen
    määrään](./asuntola_portfolio.pdf).
    

**Rahoitukseen liittyvät aiheet** 

[Kiinteistörahastojen riskien tarkastelu R:llä](./asuntorahasto_portfolio.pdf)
 



Tai kurkkaa alla oleva tiivis dashboard, joka tarjoaa nopean katsauksen Suomen julkisen talouden tilaan ja kehitykseen vuosilta 2000–2024. Tämä dashboard on toteutettu Tableau Publicilla. 


<img 
    src="https://raw.githubusercontent.com/leenaleino/portfolio/main/Dashboard%203.png" 
    alt="Dashboard Suomen julkisesta taloudesta vuosilta 2000-2024"
    style="width:60%; max-width:600px; display:block; margin:0 auto;">

### Työmahdollisuus?
Olen kiinnostunut kehittämään ja hyödyntämään osaamistani analyyttisissa tehtävissä.
Yhteyden minuun saa 📧 <a href="mailto:leena.leino@live.com">leena.leino@live.com</a>




Terveisin Leena, VTM

<!DOCTYPE html>
<html lang="fi">
<head>
  <meta charset="UTF-8" />
  <title>SP500 + Nasdaq Ticker</title>
  <style>
    .ticker-bar {
      position: fixed;
      bottom: 0;
      left: 0;
      width: 100%;
      height: 32px;
      background: #2a2a2a;
      overflow: hidden;
      z-index: 9999;
      font-family: "Roboto Mono", monospace;
      color: #ffffff;
    }

    .ticker-track {
      display: inline-block;
      white-space: nowrap;
      will-change: transform;
      animation: ticker-scroll 18s linear infinite;
      line-height: 32px;
      padding-left: 100%;
      opacity: 1;
      transition: opacity 0.6s ease;
    }

    .fade-out {
      opacity: 0;
    }

    .fade-in {
      opacity: 1;
    }

    .ticker-item { margin-right: 3rem; }
    .ticker-separator { margin: 0 1.5rem; }

    .ticker-up { color: #4caf50; }
    .ticker-down { color: #f44336; }

    @keyframes ticker-scroll {
      0% { transform: translateX(0); }
      100% { transform: translateX(-100%); }
    }
  </style>
</head>
<body>

  <div class="ticker-bar">
    <div id="tickerTrack" class="ticker-track"></div>
  </div>

  <script>
    async function fetchIndex(symbol) {
      const url = `https://query1.finance.yahoo.com/v8/finance/chart/${symbol}?interval=1m`;
      const res = await fetch(url);
      const data = await res.json();

      const meta = data.chart.result[0].meta;
      const last = meta.regularMarketPrice;
      const prev = meta.chartPreviousClose;
      const changePct = ((last - prev) / prev) * 100;

      return { value: last, changePct };
    }

    function format(name, obj) {
      const cls = obj.changePct >= 0 ? "ticker-up" : "ticker-down";
      const sign = obj.changePct >= 0 ? "+" : "";
      return `
        <span class="ticker-item">
          ${name} ${obj.value.toFixed(2)}
          <span class="${cls}">(${sign}${obj.changePct.toFixed(2)}%)</span>
        </span>
      `;
    }

    async function updateTicker() {
      const track = document.getElementById("tickerTrack");

      track.classList.add("fade-out");

      setTimeout(async () => {
        const sp = await fetchIndex("^GSPC");
        const ix = await fetchIndex("^IXIC");

        track.innerHTML = `
          ${format("S&P 500", sp)}
          <span class="ticker-separator">•</span>
          ${format("Nasdaq", ix)}
          &nbsp;&nbsp;&nbsp;
          ${format("S&P 500", sp)}
          <span class="ticker-separator">•</span>
          ${format("Nasdaq", ix)}
        `;

        track.classList.remove("fade-out");
        track.classList.add("fade-in");

        setTimeout(() => track.classList.remove("fade-in"), 600);
      }, 600);
    }

    updateTicker();
    setInterval(updateTicker, 30000);
  </script>

</body>
</html>
