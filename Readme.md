
<!-- Please update value in the {}  -->

<h1 align="center">Flag App</h1>


<div align="center">
  <h3>
    <a href="https://umit8098.github.io/Project-js-IOS-Calculator/">
      Demo
    </a>
     | 
    <a href="https://umit8098.github.io/Project-js-IOS-Calculator/">
      Project
    </a>
 
  </h3>
</div>

![Project ](./IOS-Calculator.gif)

<!-- TABLE OF CONTENTS -->

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Overview](#overview)
- [Built With](#built-with)
- [How To Use](#how-to-use)
- [About This Project](#about-this-project)
- [Contact](#contact)
- [flag-app](#flag-app)

<!-- OVERVIEW -->

## Overview
- IOS Calculator
![screenshot](project_screenshot/IOS_Calculator.gif)

---

## Built With

<!-- This section should list any major frameworks that you built your project using. Here are a few examples.-->
- JavaScript
- HTML
- CSS

## How To Use

<!-- This is an example, please update according to your application -->

To clone and run this application, you'll need [Git](https://github.com/Umit8098/Project-js-IOS-Calculator.git)


```bash
# Clone this repository
$ git clone https://github.com/Umit8098/Project-js-IOS-Calculator.git
```

## About This Project
- IOS Calculator

## Contact

<!-- - Website [your-website.com](https://{your-web-site-link}) -->
- GitHub [@Umit8098](https://github.com/Umit8098)

- Linkedin [@umit-arat](https://linkedin.com/in/umit-arat/)
<!-- - Twitter [@your-twitter](https://{twitter.com/your-username}) -->


## flag-app

1. asinc-await ile verileri çekelim
   - asinc-await ile verileri https://restcountries.com/v3.1/name/ adresinden çekelim;
   - consoldan bakıyoruz, veriler array olarak geldi. Tek elemanlı arrayin içinden 0. indexindeki ilk ve tek elemanının içindeki objelere doğrudan erişebildik. 

```js
const fetchCountry = async (name) => {
    const url = `https://restcountries.com/v3.1/name/${name}`;
    const res = await fetch(url);

    const data = await res.json();
    console.log(data[0]);
};

fetchCountry('turkey');
fetchCountry('usa');
```


2. try-catch yapısı içerisinde hataları handle edelim;

```js
const fetchCountry = async (name) => {

  const url = `https://restcountries.com/v3.1/name/${name}`;
  try {
      const res = await fetch(url);
      // if (!res.ok) throw new Error(`Something went wrong:${res.status}`);
      if (!res.ok) {
        renderError(`Something went wrong:${res.status}`);
        throw new Error();
      } 
      const data = await res.json();
      console.log(data[0]);

  } catch (error) {
    console.log(error);
    
  }

};

const renderError = (err) => {
  const countriesDiv = document.querySelector('.countries');
  countriesDiv.innerHTML = `
     <h1 class="text-danger">${err}</h1>
     <img src="./img/404.png" alt="" />
    `;
};

fetchCountry('turkey');
fetchCountry('usa');

```


3. Çektiğimiz veriyi destructure edip, bootstrap'den aldığımız bir card-component yapısı içerisinde ekrana DOM'a basalım.

```js
const fetchCountry = async (name) => {

  const url = `https://restcountries.com/v3.1/name/${name}`;
  try {
      const res = await fetch(url);
      // if (!res.ok) throw new Error(`Something went wrong:${res.status}`);
      if (!res.ok) {
        renderError(`Something went wrong:${res.status}`);
        throw new Error();
      } 
      const data = await res.json();
      // console.log(data[0]);
      renderCountry(data[0]); // gelen veriyi renderCountry fonksiyonuna gönderiyoruz.

  } catch (error) {
    console.log(error);
    
  }

};

const renderError = (err) => {
  const countriesDiv = document.querySelector('.countries');
  countriesDiv.innerHTML = `
     <h1 class="text-danger">${err}</h1>
     <img src="./img/404.png" alt="" />
    `;
};

const renderCountry = (country) => {
  console.log(country);
  const countriesDiv = document.querySelector('.countries'); // countries div'ini seçiyoruz.
  //!destructuring yapıyoruz. Gelen veriyi parçalayarak kullanıyoruz.
  const {
    capital,
    name: { common },
    region,
    flags: { svg },
    languages,
    currencies,
  } = country;

  // console.log(capital, common, region, svg);
  // console.log(Object.values(languages));
  // console.log(Object.values(currencies)[0].name);
  // console.log(Object.values(currencies)[0].symbol);
  // console.log(capital[0], common, region, svg, Object.values(languages)[0], Object.values(currencies)[0].name, Object.values(currencies)[0].symbol);

  //! countries div'ine ülke bilgilerini ekliyoruz. bootstrap kart yapısını kullanıyoruz.
  countriesDiv.innerHTML += `
    <div class="card shadow-lg" style="width: 18rem;">
      <img src="${svg}" class="card-img-top" alt="...">
      <div class="card-body">
        <h5 class="card-title">${common}</h5>
        <p class="card-text">${region}</p>
      </div>
      <ul class="list-group list-group-flush">
        <li class="list-group-item"> <i class="fas fa-lg fa-landmark"></i> ${capital}</li>
        <li class="list-group-item"> <i class="fas fa-lg fa-comments"></i> ${Object.values(languages)}</li>
        <li class="list-group-item"> <i class="fas fa-lg fa-money-bill-wave"></i> ${Object.values(currencies)[0].name} ${Object.values(currencies)[0].symbol}</li>
      </ul>
    </div>
    `;

};

fetchCountry('turkey');
fetchCountry('usa');
fetchCountry('belgium');
fetchCountry('south africa');

```



4. 
   - select-option elementi ile fetchAPI ile çektiğimiz ülkelerin name'lerini alıp combo box'ın içerisine yazdır, for döngüsü ile optionlara yazdırabiliriz.
   - select box'ın değerine göre bir event yazılıp change veya on-change olduğunda inputtaki veriyi al ve bu name'e göre country'i çağır.

```js
const countriesSelect = document.getElementById("countrySelect");

// 🔹 Tüm ülkeleri çek ve select içine ekle
const fetchAllCountries = async () => {
  const url = "https://restcountries.com/v3.1/all?fields=name";
  try {
    const res = await fetch(url);
    console.log(res); // 🔥 burada status bak
    if (!res.ok) throw new Error(`Ülkeler yüklenemedi! Status: ${res.status}`);
    const data = await res.json();
    console.log(data); // 🔥 API'den dönen datayı kontrol edelim

    // Alfabetik sıralayalım
    data.sort((a, b) => a.name.common.localeCompare(b.name.common));

    // Optionları ekle
    data.forEach((country) => {
      const option = document.createElement("option");
      option.value = country.name.common;
      option.textContent = country.name.common;
      countriesSelect.appendChild(option);
    });
  } catch (error) {
    console.log(error);
  }
};

// 🔹 Select değiştiğinde seçilen ülkeyi getir
countriesSelect.addEventListener("change", (e) => {
  const selectedCountry = e.target.value;
  if (selectedCountry) {
    document.querySelector(".countries").innerHTML = ""; // Önce temizle
    fetchCountry(selectedCountry);
  }
});


const fetchCountry = async (name) => {

  const url = `https://restcountries.com/v3.1/name/${name}`;
  try {
      const res = await fetch(url);
      // if (!res.ok) throw new Error(`Something went wrong:${res.status}`);
      if (!res.ok) {
        renderError(`Something went wrong:${res.status}`);
        throw new Error();
      } 
      const data = await res.json();
      // console.log(data[0]);
      renderCountry(data[0]); // gelen veriyi renderCountry fonksiyonuna gönderiyoruz.

  } catch (error) {
    console.log(error);
    
  }

};

const renderError = (err) => {
  const countriesDiv = document.querySelector('.countries');
  countriesDiv.innerHTML = `
     <h1 class="text-danger">${err}</h1>
     <img src="./img/404.png" alt="" />
    `;
};

const renderCountry = (country) => {
  console.log(country);
  const countriesDiv = document.querySelector('.countries'); // countries div'ini seçiyoruz.
  //!destructuring yapıyoruz. Gelen veriyi parçalayarak kullanıyoruz.
  const {
    capital,
    name: { common },
    region,
    flags: { svg },
    languages,
    currencies,
  } = country;


  //! countries div'ine ülke bilgilerini ekliyoruz. bootstrap kart yapısını kullanıyoruz.
  countriesDiv.innerHTML += `
    <div class="card shadow-lg" style="width: 18rem;">
      <img src="${svg}" class="card-img-top" alt="...">
      <div class="card-body">
        <h5 class="card-title">${common}</h5>
        <p class="card-text">${region}</p>
      </div>
      <ul class="list-group list-group-flush">
        <li class="list-group-item"> <i class="fas fa-lg fa-landmark"></i> ${capital}</li>
        <li class="list-group-item"> <i class="fas fa-lg fa-comments"></i> ${Object.values(languages)}</li>
        <li class="list-group-item"> <i class="fas fa-lg fa-money-bill-wave"></i> ${Object.values(currencies)[0].name} ${Object.values(currencies)[0].symbol}</li>
      </ul>
    </div>
    `;

};


// 🔹 Sayfa yüklenince tüm ülkeleri getir
fetchAllCountries();
```



5. 
   - çoklu seçim (multi-select) yapalım. 
   - select kutusundan birden fazla ülke seçtiğinde seçilen tüm ülkeleri ekrana getireceğiz..
   - <select>’e multiple özelliğini ekleyelim:
   - Çoklu Seçimler için Ctrl veya command tuşu ile birlikte yapılmalı. 

```js
const countriesSelect = document.getElementById("countrySelect");
const countriesDiv = document.querySelector(".countries");

// 🔹 Tüm ülkeleri çek ve select içine ekle
const fetchAllCountries = async () => {
  const url = "https://restcountries.com/v3.1/all?fields=name";
  try {
    const res = await fetch(url);
    // console.log(res); // 🔥 burada status bak
    if (!res.ok) throw new Error(`Ülkeler yüklenemedi! Status: ${res.status}`);
    const data = await res.json();
    // console.log(data); // 🔥 API'den dönen datayı kontrol edelim

    // Alfabetik sıralayalım
    data.sort((a, b) => a.name.common.localeCompare(b.name.common));

    // Optionları ekle
    countriesSelect.innerHTML = ""; // Loading yazısını temizle
    data.forEach((country) => {
      const option = document.createElement("option");
      option.value = country.name.common;
      option.textContent = country.name.common;
      countriesSelect.appendChild(option);
    });
  } catch (error) {
    console.error("Hata:", error);
  }
};


// 🔹 Çoklu seçimde seçilen ülkeleri getir
countriesSelect.addEventListener("change", async (e) => {
  const selectedOptions = Array.from(e.target.selectedOptions); // Seçilenleri al
  const selectedCountries = selectedOptions.map((opt) => opt.value);

  // Önce alanı tamamen temizle
  countriesDiv.innerHTML = "";

  // Seçilen tüm ülkeler için sırayla çağır
  for (const countryName of selectedCountries) {
    await fetchCountry(countryName);
  }

});


// 🔹 Tek ülke fetch fonksiyonu
const fetchCountry = async (name) => {

  const url = `https://restcountries.com/v3.1/name/${name}`;
  try {
      const res = await fetch(url);
      // if (!res.ok) throw new Error(`Something went wrong:${res.status}`);
      if (!res.ok) {
        renderError(`Something went wrong:${res.status}`);
        throw new Error();
      } 
      const data = await res.json();
      // console.log(data[0]);
      renderCountry(data[0]); // gelen veriyi renderCountry fonksiyonuna gönderiyoruz.

  } catch (error) {
    console.log(error);
  }
};

const renderError = (err) => {
  const errorCard = document.createElement("div");
  errorCard.innerHTML = `
     <h1 class="text-danger">${err}</h1>
     <img src="./img/404.png" alt="" />
  `;
  countriesDiv.appendChild(errorCard);
};

const renderCountry = (country) => {
  console.log(country);
  const countriesDiv = document.querySelector('.countries'); // countries div'ini seçiyoruz.
  //!destructuring yapıyoruz. Gelen veriyi parçalayarak kullanıyoruz.
  const {
    capital,
    name: { common },
    region,
    flags: { svg },
    languages,
    currencies,
  } = country;
 
  const card = document.createElement("div");
  card.classList.add("card", "shadow-lg");
  card.style.width = "18rem";

  //! countries div'ine ülke bilgilerini ekliyoruz. bootstrap kart yapısını kullanıyoruz.
  card.innerHTML = `
    <img src="${svg}" class="card-img-top" alt="${common}">
    <div class="card-body">
      <h5 class="card-title">${common}</h5>
      <p class="card-text">${region}</p>
    </div>
    <ul class="list-group list-group-flush">
      <li class="list-group-item"><i class="fas fa-lg fa-landmark"></i> ${capital}</li>
      <li class="list-group-item"><i class="fas fa-lg fa-comments"></i> ${Object.values(languages)}</li>
      <li class="list-group-item"><i class="fas fa-lg fa-money-bill-wave"></i> ${Object.values(currencies)[0].name} ${Object.values(currencies)[0].symbol}</li>
    </ul>
    `;

    countriesDiv.appendChild(card);
};

// fetchCountry('turkey');
// fetchCountry('usa');

// 🔹 Sayfa yüklenince tüm ülkeleri getir
fetchAllCountries();
```





6. 
   - Ctrl veya command tuşu olmadan Çoklu Seçim

```js
const countriesSelect = document.getElementById("countrySelect");
const countriesDiv = document.querySelector(".countries");

let renderSeq = 0; // aktif render sürümü

// 1) CTRL/CMD gerekmeden çoklu seçim: mousedown ile toggle
countriesSelect.addEventListener("mousedown", (e) => {
  const opt = e.target;
  if (opt.tagName === "OPTION") {
    e.preventDefault();            // tarayıcının varsayılan seçim davranışını durdur
    opt.selected = !opt.selected;  // kendimiz toggle edelim
    // değişikliği tetikle
    countriesSelect.dispatchEvent(new Event("change", { bubbles: true }));
  }
});

// Tüm ülkeleri çek – sadece isimler
const fetchAllCountries = async () => {
  const url = "https://restcountries.com/v3.1/all?fields=name";
  try {
    const res = await fetch(url);
    if (!res.ok) throw new Error(`Ülkeler yüklenemedi! Status: ${res.status}`);
    const data = await res.json();

    data.sort((a, b) => a.name.common.localeCompare(b.name.common));

    countriesSelect.innerHTML = "";
    data.forEach((country) => {
      const option = document.createElement("option");
      option.value = country.name.common;   // örn: "Türkiye", "United States"
      option.textContent = country.name.common;
      countriesSelect.appendChild(option);
    });
  } catch (error) {
    console.error("Hata:", error);
  }
};

// Tek ülke datasını dönen yardımcı
const fetchCountryData = async (name) => {
  const url = `https://restcountries.com/v3.1/name/${encodeURIComponent(name)}`;
  try {
    const res = await fetch(url);
    if (!res.ok) throw new Error(`Something went wrong: ${res.status}`);
    const data = await res.json();
    return data[0];
  } catch (err) {
    console.error(err);
    return null;
  }
};

const renderError = (err) => {
  const errorCard = document.createElement("div");
  errorCard.className = "text-center";
  errorCard.innerHTML = `
    <h1 class="text-danger fs-4">${err}</h1>
    <img src="./img/404.png" alt="" style="max-width:200px"/>
  `;
  countriesDiv.appendChild(errorCard);
};

const renderCountry = (country) => {
  if (!country) return;

  const {
    capital,
    name: { common },
    region,
    flags: { svg },
    languages,
    currencies,
  } = country;

  const capitalStr = Array.isArray(capital) ? capital[0] : capital || "-";
  const langs = languages ? Object.values(languages).join(", ") : "-";
  const currObj = currencies ? Object.values(currencies)[0] : null;
  const currStr = currObj ? `${currObj.name} ${currObj.symbol || ""}` : "-";

  const card = document.createElement("div");
  card.classList.add("card", "shadow-lg");
  card.style.width = "18rem";

  card.innerHTML = `
    <img src="${svg}" class="card-img-top" alt="${common}">
    <div class="card-body">
      <h5 class="card-title">${common}</h5>
      <p class="card-text">${region || "-"}</p>
    </div>
    <ul class="list-group list-group-flush">
      <li class="list-group-item"><i class="fas fa-lg fa-landmark"></i> ${capitalStr}</li>
      <li class="list-group-item"><i class="fas fa-lg fa-comments"></i> ${langs}</li>
      <li class="list-group-item"><i class="fas fa-lg fa-money-bill-wave"></i> ${currStr}</li>
    </ul>
  `;

  countriesDiv.appendChild(card);
};

// Çoklu seçim değiştiğinde render et
countriesSelect.addEventListener("change", async (e) => {
  const thisSeq = ++renderSeq; // yeni bir render sürümü başlıyor
  const selected = Array.from(e.target.options)
    .filter((o) => o.selected)
    .map((o) => o.value);

  countriesDiv.innerHTML = "";

  if (selected.length === 0) return;

  try {
    // Paralel fetch + sıralı render (seçim sırasına göre)
    const results = await Promise.all(selected.map((name) => fetchCountryData(name)));

    // bu arada başka bir seçim olduysa, bu render'ı iptal et
    if (thisSeq !== renderSeq) return;

    results.forEach((cty) => {
      if (!cty) renderError("Ülke alınamadı.");
      else renderCountry(cty);
    });
  } catch (err) {
    if (thisSeq !== renderSeq) return;
    renderError("Bir şeyler ters gitti.");
  }
});

// sayfa açılışında listeyi doldur
fetchAllCountries();
```
