# 💧 Liquidity Flow Dashboard

**Akışkan Para Rejim Modeli - Şeffaf, Gerçekçi, Denetlenebilir**

[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

---

## 🎯 Proje Amacı

Bu proje, finansal piyasalardaki **likidite rejimlerini** (risk ortamını) **fizik benzetmesi** ile modelleyen, **Bayesian olasılık** tabanlı, **açıklanabilir** bir analiz sistemidir.

**Hedef**: %100 doğru tahmin değil; **tutarlı teori**, **ölçülebilir sinyaller**, **kalibre olasılıklar** ve **şeffaf açıklamalar**.

---

## 🧠 Temel Teori: Akışkan Para Modeli

Para, rezervuarlar (varlık sınıfları) arasında akan bir **akışkan** olarak modellenir.

### Üç Fiziksel Boyut

| Boyut | Tanım | Proxy'ler |
|-------|-------|-----------|
| **P (Basınç)** | Sistemi iten/çeken güçler<br>Finansman koşulları, likidite bolluğu | Fed Funds rate velocity<br>USD strength<br>Yield curve slope<br>Funding stress (TED spread) |
| **μ (Viskozite)** | Akışa direnç<br>Belirsizlik, volatilite | Realized volatility<br>Drawdown depth<br>Credit spreads<br>Cross-asset correlation |
| **Q (Debi)** | Net akış büyüklüğü<br>Risk iştahı | Equity momentum<br>Defensive/Cyclical ratio<br>Crypto beta<br>Put/Call ratio |

### 5 Latent Rejim

```
RISK_ON              → Güçlü risk iştahı, düşük vol
LATE_RISK_ON         → Aşırı ısınma sinyalleri  
NEUTRAL              → Kararsız, mixed signals
DEFENSIVE            → Koruyucu pozisyonlama
FLIGHT_TO_LIQUIDITY  → Panik, likidite kaçışı
```

---

## 🔬 Metodoloji

### Bayesian Framework

```
P(rejim | sinyaller) ∝ P(sinyaller | rejim) · P(rejim)
```

- **Likelihood**: Gaussian profil - her rejim için beklenen sinyal paterni
- **Prior**: Statik başlangıç + Markov geçiş matrisi
- **Posterior**: Softmax normalize

### Kalibrasyon (İki Katmanlı)

1. **Empirical Calibration**
   - 10-15 tarihsel dönem manuel etiketlenir
   - Her rejim için sinyal profili (μ, σ) güncellenir
   
2. **Outcome-Based Evaluation**
   - Forward returns ile doğrulama
   - Brier score, calibration plots
   - Basit backtest metrikleri

---

## 🏗️ Proje Yapısı

```
liquidity-flow-dashboard/
├── src/
│   ├── data/
│   │   ├── fetchers.py          # Veri çekme (yfinance, FRED)
│   │   ├── processors.py        # Sinyal hesaplama (P, μ, Q)
│   │   └── storage.py           # SQLite veritabanı
│   ├── models/
│   │   ├── regime_profiles.py   # Rejim profilleri (μ_r, σ_r)
│   │   ├── bayesian_engine.py   # Posterior hesaplama
│   │   ├── calibration.py       # Empirical kalibrasyon
│   │   └── priors.py            # Prior yönetimi
│   ├── explainability/
│   │   ├── evidence_breakdown.py   # Log posterior decomposition
│   │   ├── signal_attribution.py   # Top contributing signals
│   │   └── confidence_metrics.py   # Entropy, calibration
│   ├── evaluation/
│   │   ├── outcome_based.py     # Forward return evaluation
│   │   ├── brier_score.py       # Kalibrasyon metrikleri
│   │   └── backtest.py          # Basit strateji backtest
│   └── dashboard/
│       ├── app.py               # Streamlit ana uygulama
│       └── components/          # UI bileşenleri
├── tests/
│   ├── test_signals.py
│   ├── test_bayesian.py
│   └── test_calibration.py
├── data/
│   ├── raw/                     # Ham veri (git'e eklenmez)
│   ├── processed/               # İşlenmiş sinyaller
│   └── calibration/             # Tarihsel etiketler
├── configs/
│   ├── regime_profiles.yaml     # Rejim tanımları
│   ├── signal_weights.yaml      # P, μ, Q ağırlıkları
│   └── calibration_periods.yaml # Manuel etiketli dönemler
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   ├── 02_signal_engineering.ipynb
│   └── 03_calibration_analysis.ipynb
└── .github/
    └── workflows/
        ├── ci.yml               # Test + lint
        └── data_update.yml      # Günlük veri güncelleme
```

---

## 🚀 Kurulum

### Gereksinimler

- Python 3.10+
- FRED API Key (ücretsiz: https://fred.stlouisfed.org/docs/api/api_key.html)

### Adımlar

```bash
# 1. Repo'yu klonla
git clone https://github.com/KULLANICI_ADIN/liquidity-flow-dashboard.git
cd liquidity-flow-dashboard

# 2. Virtual environment oluştur
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Bağımlılıkları yükle
pip install -r requirements.txt

# 4. Çevre değişkenlerini ayarla
cp .env.example .env
# .env dosyasına FRED_API_KEY'ini ekle

# 5. İlk veriyi çek
python src/data/fetchers.py

# 6. Dashboard'u başlat
streamlit run src/dashboard/app.py
```

---

## 📊 Dashboard Özellikleri

### 1. Rejim Olasılıkları
- Real-time 5 rejim olasılık dağılımı
- Trend göstergeleri (↗ ↘ →)
- Güven skoru (entropy tabanlı)

### 2. Evidence Breakdown
- Log prior, log likelihood, log posterior tablosu
- Her rejim için unnormalized değerler
- "Model neden bunu söylüyor?" sorusunun cevabı

### 3. Top Contributing Signals
- Her rejim için en güçlü 5 sinyal
- (x_i - μ_{r,i})² decomposition
- Uyum/uyumsuzluk göstergeleri

### 4. Sinyal Gösterge Paneli
- P, μ, Q ana metrikleri
- Alt sinyaller (Rate velocity, USD pressure, etc.)
- 7-gün değişim trendleri

### 5. Kalibrasyon Metrikleri
- Brier score
- Calibration plots (reliability diagram)
- Outcome-based doğrulama

---

## 🧪 Test ve Doğrulama

```bash
# Tüm testleri çalıştır
pytest tests/ -v

# Coverage raporu
pytest tests/ --cov=src --cov-report=html

# Linting
flake8 src/ --max-line-length=100
black src/ --check
```

---

## 📈 Örnek Kullanım

```python
from src.data.processors import calculate_all_signals
from src.models.bayesian_engine import RegimeModel

# Sinyalleri hesapla
signals = calculate_all_signals(
    equity_data=equity_df,
    bond_data=bond_df,
    macro_data=macro_df
)

# Model oluştur ve posterior hesapla
model = RegimeModel()
regime_probs = model.calculate_posterior(signals)

print(regime_probs)
# {
#     'RISK_ON': 0.45,
#     'LATE_RISK_ON': 0.12,
#     'NEUTRAL': 0.18,
#     'DEFENSIVE': 0.15,
#     'FLIGHT_TO_LIQUIDITY': 0.10
# }
```

---

## 🎓 Akademik Referanslar

1. **Market Regimes**: Ang, A., & Bekaert, G. (2002). "Regime Switches in Interest Rates"
2. **Liquidity Models**: Amihud, Y. (2002). "Illiquidity and Stock Returns"
3. **Bayesian Methods**: Hamilton, J. D. (1989). "A New Approach to the Economic Analysis of Nonstationary Time Series"

---

## ⚠️ Önemli Notlar

### Bu Model Ne Yapabilir?
✅ Mevcut piyasa rejimini %60-70 doğrulukla tanımlayabilir  
✅ Rejim geçişlerinin erken sinyallerini yakalayabilir  
✅ Risk yönetimi için actionable insight'lar sağlar  
✅ "Neden bu rejim?" sorusunu yanıtlayabilir  

### Bu Model Ne YAPAMAZ?
❌ Gelecek haftaki rejimi %90 kesinlikle tahmin edemez  
❌ "Kesin al/sat" sinyali veremez  
❌ Black swan olayları öngöremez  

**Bu bir akademik/araştırma projesidir - ticari kullanım tavsiyesi değildir.**

---

## 🤝 Katkıda Bulunma

1. Fork edin
2. Feature branch oluşturun (`git checkout -b feature/amazing-feature`)
3. Commit edin (`git commit -m 'Add amazing feature'`)
4. Push edin (`git push origin feature/amazing-feature`)
5. Pull Request açın

---

## 📝 Lisans

MIT License - detaylar için [LICENSE](LICENSE) dosyasına bakın.

---

## 📧 İletişim

Proje Sahibi - [@github_username](https://github.com/github_username)

Proje Linki: [https://github.com/github_username/liquidity-flow-dashboard](https://github.com/github_username/liquidity-flow-dashboard)

---

## 🙏 Teşekkürler

- Anthropic Claude - Proje tasarım desteği
- FRED API - Makroekonomik veri
- Streamlit - Dashboard framework
