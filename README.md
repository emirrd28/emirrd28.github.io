<!DOCTYPE html>
<html lang="tr">
<head>
<meta charset="UTF-8">
<title>Adam Asmaca</title>
<style>
    body { font-family: Arial, sans-serif; text-align: center; margin-top: 20px; }
    #wordDisplay { font-size: 24px; letter-spacing: 2px; margin: 20px; }
    #hangmanCanvas { border: 1px solid #000; background: #f9f9f9; margin: 20px auto; display: block; }
    .hint { margin: 5px; font-style: italic; }
    input { padding: 5px; font-size: 16px; }
    button { padding: 5px 10px; font-size: 16px; }
</style>
</head>
<body>

<h1>Adam Asmaca</h1>
<canvas id="hangmanCanvas" width="200" height="200"></canvas>

<div id="wordDisplay"></div>

<div>
    <input type="text" id="letterInput" maxlength="1" placeholder="Harf tahmin et">
    <button onclick="guessLetter()">Tahmin Et</button>
</div>

<div>
    <input type="text" id="wordInput" placeholder="Kelimeyi tahmin et">
    <button onclick="guessWord()">Kelimeyi Tahmin Et</button>
</div>

<div class="hint" id="hint1"></div>
<div class="hint" id="hint2"></div>

<div>Hatalı Tahminler: <span id="wrongLetters"></span></div>

<script>
// Örnek 50 kelime (Bunu 1000'e kadar kolayca genişletebilirsin)
const words = [
    {word: "elma", hints: ["Bir meyve", "Kırmızı veya yeşil olabilir"]},
    {word: "araba", hints: ["Taşıma aracı", "Dört tekerlekli"]},
    {word: "bilgisayar", hints: ["Elektronik cihaz", "Kod yazmak için kullanılır"]},
    {word: "deniz", hints: ["Büyük su kütlesi", "Dalgalar vardır"]},
    {word: "kalem", hints: ["Yazı aracı", "Mürekkepli olabilir"]},
    {word: "telefon", hints: ["İletişim aracı", "Akıllı veya klasik olabilir"]},
    {word: "köpek", hints: ["Evcil hayvan", "Sadık bir dost"]},
    {word: "dağ", hints: ["Yüksek yer", "Tırmanılabilir"]},
    {word: "gitar", hints: ["Müzik aleti", "Altı teli vardır"]},
    {word: "kitap", hints: ["Okuma materyali", "Sayfaları vardır"]},
    {word: "uçak", hints: ["Havada uçar", "Motorları vardır"]},
    {word: "şehir", hints: ["İnsanlar yaşar", "Evler ve binalar vardır"]},
    {word: "kedi", hints: ["Evcil hayvan", "Tüyleri vardır"]},
    {word: "müzik", hints: ["Ruhun gıdası", "Dinlenir"]},
    {word: "masa", hints: ["Üzerinde çalışılır", "Dört ayağı vardır"]},
    {word: "sandalye", hints: ["Oturmak için", "Dört ayağı vardır"]},
    {word: "çanta", hints: ["Eşyaları taşımak için", "Omuzda taşınır"]},
    {word: "bardak", hints: ["İçmek için", "Genellikle camdan yapılır"]},
    {word: "şemsiye", hints: ["Yağmurdan korur", "Açılır kapanır"]},
    {word: "gözlük", hints: ["Görmeyi kolaylaştırır", "Camlı bir aksesuar"]},
    {word: "bilezik", hints: ["Takı", "Bileğe takılır"]},
    {word: "saat", hints: ["Zamanı gösterir", "Kol saatleri vardır"]},
    {word: "balık", hints: ["Suda yaşar", "Yüzgeçleri vardır"]},
    {word: "çorap", hints: ["Ayağa giyilir", "Genellikle pamuktan"]},
    {word: "ayakkabı", hints: ["Ayağı korur", "Dışarıda giyilir"]},
    {word: "eldiven", hints: ["Eller için", "Soğuktan korur"]},
    {word: "araba", hints: ["Motorlu taşıt", "Yolda gider"]},
    {word: "tren", hints: ["Raylarda gider", "Vagonları vardır"]},
    {word: "otobüs", hints: ["Toplu taşıma", "Yolcular taşır"]},
    {word: "deniz", hints: ["Büyük su kütlesi", "Tuzludur"]},
    {word: "güneş", hints: ["Gökyüzünde parlar", "Isı ve ışık verir"]},
    {word: "ay", hints: ["Gece gökyüzü", "Dünya etrafında döner"]},
    {word: "yıldız", hints: ["Gökyüzünde parlar", "Gece görünür"]},
    {word: "el", hints: ["Kavramak için", "Beş parmağı vardır"]},
    {word: "ayak", hints: ["Yürümek için", "İki tane vardır"]},
    {word: "kulak", hints: ["Duyma organı", "İki tane vardır"]},
    {word: "burun", hints: ["Koklamak için", "Ortada yer alır"]},
    {word: "göz", hints: ["Görme organı", "İki tane vardır"]},
    {word: "diş", hints: ["Yemekleri çiğnemek için", "Ağızda bulunur"]},
    {word: "dil", hints: ["Konuşmak için", "Ağızda bulunur"]},
    {word: "saç", hints: ["Başta çıkar", "Uzun veya kısa olabilir"]},
    {word: "çocuk", hints: ["Küçük insan", "Oyun oynamayı sever"]},
    {word: "yetişkin", hints: ["Büyümüş insan", "Sorumlulukları vardır"]},
    {word: "öğretmen", hints: ["Bilgi verir", "Sınıfta çalışır"]},
    {word: "öğrenci", hints: ["Bilgi öğrenir", "Sınıfta oturur"]},
    {word: "spor", hints: ["Fiziksel aktivite", "Sağlık için iyi"]},
    {word: "futbol", hints: ["Top ile oynanır", "İki takım vardır"]},
    {word: "basketbol", hints: ["Potaya atılır", "Top ile oynanır"]},
    {word: "yüzme", hints: ["Suda yapılan spor", "Havuzda veya denizde"]},
    {word: "koşu", hints: ["Hareketli spor", "Ayakları kullanılır"]},
    {word: "dağcılık", hints: ["Dağa tırmanma", "Zorlu bir spor"]},
];

let selected = words[Math.floor(Math.random()*words.length)];
let guessedLetters = [];
let wrongLetters = [];
let mistakes = 0;
const maxMistakes = 6;

document.getElementById("hint1").innerText = "İpucu 1: " + selected.hints[0];
document.getElementById("hint2").innerText = "İpucu 2: " + selected.hints[1];

function displayWord() {
    let display = "";
    for (let char of selected.word) {
        if (guessedLetters.includes(char)) {
            display += char + " ";
        } else {
            display += "_ ";
        }
    }
    document.getElementById("wordDisplay").innerText = display.trim();
}

function drawHangman() {
    const canvas = document.getElementById("hangmanCanvas");
    const ctx = canvas.getContext("2d");
    ctx.clearRect(0,0,canvas.width,canvas.height);

    // Direk
    ctx.beginPath();
    ctx.moveTo(10,190);
    ctx.lineTo(190,190);
    ctx.moveTo(50,190);
    ctx.lineTo(50,10);
    ctx.lineTo(150,10);
    ctx.lineTo(150,30);
    ctx.stroke();

    // Adam
    if (mistakes > 0) { ctx.beginPath(); ctx.arc(150,40,10,0,Math.PI*2); ctx.stroke(); } // kafa
    if (mistakes > 1) { ctx.beginPath(); ctx.moveTo(150,50); ctx.lineTo(150,100); ctx.stroke(); } // gövde
    if (mistakes > 2) { ctx.beginPath(); ctx.moveTo(150,60); ctx.lineTo(130,80); ctx.stroke(); } // sol kol
    if (mistakes > 3) { ctx.beginPath(); ctx.moveTo(150,60); ctx.lineTo(170,80); ctx.stroke(); } // sağ kol
    if (mistakes > 4) { ctx.beginPath(); ctx.moveTo(150,100); ctx.lineTo(130,130); ctx.stroke(); } // sol bacak
    if (mistakes > 5) { ctx.beginPath(); ctx.moveTo(150,100); ctx.lineTo(170,130); ctx.stroke(); } // sağ bacak
}

function guessLetter() {
    const input = document.getElementById("letterInput");
    const letter = input.value.toLowerCase();
    input.value = "";
    if (!letter || guessedLetters.includes(letter) || wrongLetters.includes(letter)) return;

    if (selected.word.includes(letter)) {
        guessedLetters.push(letter);
    } else {
        mistakes++;
        wrongLetters.push(letter);
        document.getElementById("wrongLetters").innerText = wrongLetters.join(", ");
    }
    drawHangman();
    displayWord();
    checkGameOver();
}

function guessWord() {
    const input = document.getElementById("wordInput");
    const wordGuess = input.value.toLowerCase();
    input.value = "";
    if (wordGuess === selected.word) {
        guessedLetters = [...new Set(selected.word.split(""))];
    } else {
        mistakes++;
    }
    drawHangman();
    displayWord();
    checkGameOver();
}

function checkGameOver() {
    if (!document.getElementById("wordDisplay").innerText.includes("_")) {
        alert("Tebrikler! Kazandınız 🎉");
        location.reload();
    } else if (mistakes >= maxMistakes) {
        alert("Kaybettiniz! Kelime: " + selected.word);
        location.reload();
    }
}

// Başlangıç
displayWord();
drawHangman();
</script>

</body>
</html>
