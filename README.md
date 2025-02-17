[Machine Learning Link](https://drive.google.com/drive/folders/1LgYG7lV5x594Pm6KQRFvolBa8fCYoqz-?usp=sharing)  
[Dashboard Flask](https://drive.google.com/drive/folders/1LgYG7lV5x594Pm6KQRFvolBa8fCYoqz-?usp=sharing)

# Final-Project-Melbourne-Housing-Market

### by Satrio Dirgantoro (JCDS 10 Jakarta)

[![photo-1593255397598-dff69d32bb73-ixlib-rb-1-2.jpg](https://i.postimg.cc/6QtgQTK7/photo-1593255397598-dff69d32bb73-ixlib-rb-1-2.jpg)](https://postimg.cc/LJQNC9nR)

## License
The dataset was taken from Kaggle under [License](https://creativecommons.org/licenses/by-nc-sa/4.0/)

## Background

Dataset diambil dari [Kaggle](https://www.kaggle.com/anthonypino/melbourne-housing-market?select=MELBOURNE_HOUSE_PRICES_LESS.csv) yang sumber datanya berasal dari [Domain](Domain.com.au) bagian dari Domain Group. Domain group adalah sebuah perusahaan yang menawarkan ekosistem solusi properti multi-platform.
[Domain](Domain.com.au) sendiri merupakan website yang menampilkan property di Australia yang dijual dengan sistem lelang. 
Istilah “housing price bubble” atau “harga perumahan yang menggelembung” seringkali digunakan di banyak situasi tapi sangat jarang sekali didefinisikan dengan jelas. Case and Shiller (2003) percaya bahwa istilah bubble menggambarkan kondisi dimana masyarakat atau pelaku pasar secara berlebihan berharap (excessive public expectations) bahwa akan ada kenaikan harga di masa datang karena sebelumnya terjadi kenaikan harga terus menerus. Secara umum housing bubble dapat didefinisikan sebagai kenaikan harga properti atau sekelompak asset yang berlangsung terus menerus, dimana kenaikan harga tersebut akan menyebabkan muncunya harapan kenaikan harga dimasa yang akan datang. 


## Problems
Dalam kondisi seperti ini, tentu akan lebih sulit untuk para vendor atau property owner untuk menentukan harga yang tepat yang sesuai dengan market agar tidak menjual harga properti terlalu mahal sehingga sulit untuk dijual dan juga tidak terlalu murah agar tidak kehilangan profit.


## Business Questions
- Kapan housing bubble terjadi di Melbourne?
- Apakah jarak suatu property ke CBD area berpengaruh terhadap harga?
- Di daerah manakah yang memiliki potensi penjualan tertinggi?
- Fitur - fitur apa saja yang menjadi faktor penting dalam menentukan harga suatu rumah?

## Goals
Dalam Project ini, saya akan menganalisis harga properti, lebih spesifiknya harga property di Melbourne dan melihat fitur fitur apa saja yang mempengaruhi harga dari suatu rumah dan membuat model machine learning dengan pendekatan Supervised Learning: Regression untuk memprediksi harga rumah berdasarkan fitur fiturnya.

## Steps
Handling Missing Values [File Handling Missing Values](https://github.com/sadirga/Final-Project-Melbourne-House-Price/blob/main/Melbourne%20Housing%20Handling%20Missing%20Values%20Interpolate.ipynb)  
Exploratory Data Analysis [EDA](https://github.com/sadirga/Final-Project-Melbourne-House-Price/blob/main/MELBOURN%20HOUSING%20EDA%20Interpolate.ipynb)  
Feature Selection  
Feature Engineering  
Machine Learning Modelling with Support Vector Machine Regressor, Random Forest Regressor dan XGBoost Regressor, optimizing model with Scalling and Hyper Parameter Tuning [Machine Learning Modelling](https://github.com/sadirga/Final-Project-Melbourne-House-Price/blob/main/Melbourne%20Housing%20Market%20Supervised%20Learning.ipynb)  
Evaluation Metrics  
Conclusion  

## EDA
Price Distribution  
[![download.png](https://i.postimg.cc/ZYdM1GpW/download.png)](https://postimg.cc/MXz5Brsw)

Amount of House Sold Yearly  
[![download-1.png](https://i.postimg.cc/QxgnMc6s/download-1.png)](https://postimg.cc/F7RxWk5n)

Landsize vs Price  
[![download-2.png](https://i.postimg.cc/zXk0N8BG/download-2.png)](https://postimg.cc/qzz8XfyP)

BuildingArea vs Price  
[![download-3.png](https://i.postimg.cc/2ScGqGnp/download-3.png)](https://postimg.cc/Xr5dmw2x)

## Modelling

Evaluation Matrix  

```bash
    +-----------+----------+----------+----------+-----------+
    |  Model    |   MAE    |   MSE    |   RMSE   |     R2    |
    +-----------+----------+----------+----------+-----------+
    | RF_Tuned  | 0.147695 | 0.040030 | 0.200074 |  0.846322 |
    | SVM_Tuned | 0.151600 | 0.042085 | 0.205145 |  0.838433 |
    | XGB Tuned | 0.155412 | 0.043089 | 0.207579 |  0.834576 |
    | Base XGB  | 0.157556 | 0.043582 | 0.208763 |  0.832685 |
    | Base RF   | 0.154578 | 0.043927 | 0.209587 |  0.831360 | 
    | Base SVM  | 0.159214 | 0.045543 | 0.213408 |  0.825155 | 
    +-----------+----------+----------+----------+-----------+
```


  ### Conclusion
  Model Machine Learning terbaik untuk memprediksi Harga Properti di Melbourne adalah Random Forest yang sudah melalui proses Hyper Parameter Tuning dengan tingkat akurasi 85%     dan dengan tingkat error yang paling kecil dibanding model machine learning lainnya.

## Pipeline Best Model
```python
X_pipe = df2.drop(columns=['Price']) ### Features / Soal
y_pipe = df2['Price']
y_pipe = np.log(y_pipe)
X_train_pipe, X_test_pipe, y_train_pipe, y_test_pipe = train_test_split(X_pipe, y_pipe, test_size = .2, random_state = 2, )

cat_columns = ['Type','Suburb']
num_columns = ['Rooms', 'Distance', 'Bathroom','Landsize','BuildingArea']

categorical_pipeline = Pipeline([
    ('encoder', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer([
    ('categoric', categorical_pipeline, cat_columns)
])

pipe_RF = Pipeline([
    ('prep', preprocessor),
    ('algo', RandomForestRegressor())
])

param_RF_pipe = {'algo__max_depth': [None],
                 'algo__max_features': [5],
                 'algo__min_samples_split': [2],
                 'algo__n_estimators': [2000]}

model_RF_pipe = GridSearchCV(estimator=pipe_RF, param_grid=param_RF_pipe, cv=5, n_jobs=-1, verbose=1)
model_RF_pipe.fit(X_train_pipe, y_train_pipe)
model_RF_pipe_tuned = model_RF_pipe.best_estimator_
y_pred = model_RF_pipe_tuned.predict(X_test_pipe)

## Deploy
import joblib
joblib.dump(pipe_RF,'MelbourneModel')
```

## Dashboard
[![Screenshot-3.png](https://i.postimg.cc/PJxgGkW2/Screenshot-3.png)](https://postimg.cc/r0BnW3Yr)

[![Screenshot-5.png](https://i.postimg.cc/05XgYbv3/Screenshot-5.png)](https://postimg.cc/YjFyk2hf)

[![Screenshot-11.png](https://i.postimg.cc/W4vxNZmD/Screenshot-11.png)](https://postimg.cc/NKddb5zY)

[![Screenshot-6.png](https://i.postimg.cc/XJ2PkDRF/Screenshot-6.png)](https://postimg.cc/8J6w1wPP)

[![Screenshot-2.png](https://i.postimg.cc/GhpLRW30/Screenshot-2.png)](https://postimg.cc/ThZMqHWQ)

[![Screenshot-1.png](https://i.postimg.cc/mDNLZRF4/Screenshot-1.png)](https://postimg.cc/cgCyTpmk)


## CONCLUSION

<br>

**Data Distribution**  

Pada dataset Melbourne Housing Market ini terdapat 12 kolom numerical dan 7 kolom categorical. Berdasarkan kolom target tampak dsitribusinya skewness positif, ini menandakan, sebagian besar distribusi berada di nilai rendah dan nilai rata-rata nya diatas nilai median. Hal ini juga menunjukkan bahwa kebanyakan dari rumah yang dijual harganya dibawah rata-rata/median dan ada beberapa rumah yang terjual dengan harga yang sangat tinggi jauh melebihi harga pasaran. Hal ini tentu membuat property owner semakin sulit menentukan harga yang pas/fair untuk propertinya.

<br>

**Analisis**  

Dari dataset ini, terlihat bahwa median price property dari tahun ke tahun cenderung turun, namun terjadi lonjakan permintaan/demand terhadap property pada tahun 2017 hingga 92% dibanding tahun 2016. Demand/permintaan/penjualan properti di tahun 2017 meningkat drastis dari bulan Maret sampai dengan November, khususnya di bulan 9 atau September penjualan meningkat 285% dibanding tahun 2016 di bulan yang sama. Ini menandakan, Housing bubble mencapai puncak nya di tahun 2017 sebelum akhirnya demand melambat di bulan Desember 2017.
Jumlah kamar dan kamar mandi yang merupakan fitur utama dari sebuah rumah memang mempengaruhi harga dari sebuah rumah. Sedangkan dari jumlah space untuk parker mobil tidak terlalu berpengaruh banyak terhadap harga dari suatu rumah. Luas bangunan juga menjadi salah satu faktor yang mempengaruhi harga dari suatu rumah, umur dari sebuah rumah bukanlah faktor yang berpengaruh pada suatu harga rumah,karena sebuah rumah dapat direnovasi pada akhirnya.
Berdasarkan hasil folium map, terlihat bahwa rumah rumah yang harganya dibawah harga median atau dengan kisaran harga 500k - 1M letaknya berada jauh dari CBD, sedangkan rumah dengan kisaran harga 4 - 10M atau bisa di bilang sangat mahal berada disekitaran/dekat dengan CBD area.
Dari peta pebaran tersebut bahwa beberapa daerah/suburb walaupun jaraknya tidak terlalu dekat namun median price nya sangat tinggi seperti Canterbury yang median price nya sangat tinggi padahal distancenya dari CBD 9.0 km.
Hal ini tentu mengindikasikan bahwa jarak rumah dari CBD area adalah salah satu faktor penting dalam menentukan rumah tetapi daerah/suburbs dari sebuah rumah merupakan hal yang lebih penting dalam menentukan harga suatu rumah.  
Reservoir adalah daerah suburbs yang paling diminati pembeli, karena median pricenya yang sangat terjangkau dan tempatnya yang sudah ramai/developed dan memiliki berbagai fasilitas dan tempat hiburan. Sedangkan Canterbury menjadi daerah yang paling tinggi median pricenya, karena memang daerah tersebut merupakan perumahan yang ekslusif.
<br>

## SUGGESTION

- Cek lokasi property anda terlebih dahulu, apakah lokasi anda tersebut termasuk dalam daerah suburb yang median price rumahnya memang tinggi atau tidak. Karena lokasi daerah/suburb berpengaruh terhadap harga properti.

- Umur sebuah property bukanlah hal yang siginifikan untuk menentukan harga property anda.

- Jarak Property Anda dengan CBD merupakah hal yang sangat mempengaruhi sebuah harga, jika jarak property anda ke CBD area berada dibawah 10 KM maka median Price nya adalah AUD 975,000.

- Untuk menaikkan peluang anda dalam menjual property dengan harga yang tinggi, disarankan agar menggunakan jasa Property Agent Marshall, Jellis atau Buxton karena portfolio mereka yang berhasil menjual property diatas 800 buah dengan nilai median price diatas 1 juta AUD.


