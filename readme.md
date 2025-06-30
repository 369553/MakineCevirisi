# Sinirsel Makine Çevirisi (Neural Machine Translation)

### BU DEPO HAKKINDA

- Bu depoda, yinelemeli sinir ağıyla İngilizce'den Türkçe'ye çeviri yapan bir model geliştirilmiştir.

- Pek çok yapı ve ön işlem titizlikle uygulanmıştır.

- Bu belgede hem projenin nasıl kullanılacağı, hem kullanılan yapıların ne anlama geldiği ve neden seçildiği, hem de sinirsel makine çevirisinin temelleri anlatılmak istenmiştir.

- Dilerseniz belgeyi baştan ona okuyup, bu alanı tanıyabilir, dilerseniz de kodlardaki hiperparametrelerin ne anlama geldiği ve kodun nasıl çalıştırılabileceği kısmına atlayabilirsiniz.

- Kod dosyası içerisinde ilgili yerlere doyurucu yorumlar yazılmıştır; her parçanın bütün içerisindeki rolünü daha iyi anlamak için bu notları inceleyiniz.

- Hiperparametreler kodun en üstünde belirtilmiş; dinamik ve esnek bir yapı oluşturulmuştur.

- Modelinizi oluşturmak için güçlü işlem gücüne ve hâfızaya ihtiyacınız vardır; 16 GB'tan az RAM belleğine sâhipseniz tüm veri setini kullanmanız mümkün olmayacaktır; verinizi RAM belleğe parça parça yüklemek pek etkili bir çözüm olmaz; çünkü ağ içerisinde verinizdeki kelîme sayısı adedince sinir hücresi barındıran katmanlarınız vardır (fazla cümle fazla kelîme sayısı ve o miktarda sinir hücresi barındıran ağ katmanı demektir).

### KULLANIM KILAVUZU

- Eğer yeni bir model eğitmek istemiyorsanız, az eğitilmiş modeli kullanmak istiyorsanız kodu olduğu gibi çalıştırabilirsiniz; fakat şu adresten dil modelini indirip, projenin bulunduğu dizine "trmodel" ismiyle eklemelisiniz:
  [https://github.com/akoksal/Turkish-Word2Vec?tab=readme-ov-file

- Yeni bir model eğitmek için kodun başlarında bulunan **Hiperparametreler** kısmındaki `trainModel` parametresine `True` değerini veriniz.

- "nmt_v2.weights.h5" dosyasındaki model ağırlıklarıyla belirtilen model, 400 bin cümle üzerinde 196 GRU hücre sayısı ve 100 kelîme gömme boyutuyla 8-9 tur (epoch) kadar eğitilmiştir.

- Eğer yüksek RAM ve işlem gücüne sâhip bir cihazınız varsa ve kaliteli model oluşturmak istiyorsanız GRU hücre sayısını ve tur sayısını arttırabilirsiniz.

- Veri seti yaklaşık 713 bin cümleden oluşuyor; bunların çoğu kısa cümleler. Cümleler uzunluğuna göre sıralanmıştır ve 400 bin cümle için eğitim yapılmıştır.

- Proje kapsamındaki hiperparametreler şu şekildedir (bunlara kod içerisinde de değinilmiştir):
  
  - `trainModel = False` : "Modeli eğit" bayrağıdır.
  
  - `useAllDataSet = False` : Tüm veri setinin kullanılması isteniyorsa `True` verilmelidir.
  
  - `limitCountOfSentences=400000` : Tüm veri setinin kullanılması istenmiyorsa, kaç cümlenin içeriye aktarılması gerektiği bu parametreyle belirtilir.
  
  - `embeddingSize = 100` : Kelîme vektör boyutudur. Çeviri işlemi çetrefilli bir işlem olduğundan, 100'den aşağı verilmemesi uygundur. `100` değeri ise başlangıç için uygundur; eğer daha yüksek boyut istiyorsanız, kullanılan İngilizce dil modelinin yüksek boyutlu olanını indirmelisiniz. Eğer daha düşük boyut istiyorsanız, kod içerisinde vektörleri küçülten bir kısım bulunmaktadır; kelîme vektörleri buraya yazdığınız değere indirgenmektedir.
  
  - `useManuelMaxLengthForSentences = False` : Doğal dil işlemede modeller sâbit uzunluklu girdiler almaktadır. Kısa cümleler verilen uzunluğa tamâmlanır (padding), uzun cümleler ise kısaltılır (truncating). Bu parametreye `False` değeri verildiğinde cümle uzunluğu veri setindeki en uzun cümle olacak şekilde ayarlanır. Veri seti aşırı uzun cümlelerden temizlendiği için elle belirtmeniz tavsiye edilmez; fakat deneysel çalışmalar için değiştirebilirsiniz.
  
  - `maxLengthForTRSentences` : Cümle uzunluğu elle belirtildiğinde Türkçe cümleler için cümle uzunluğu (kelîme cinsinden) bu parametreye göre ayarlanır.
  
  - `maxLengthForENGSentences` : Cümle uzunluğu elle belirtildiğinde İngilizce cümleler için cümle uzunluğu (kelîme cinsinden) bu parametreye göre ayarlanır.
  
  - `loadEmbeddingWeightsFromFiles = True`  : Bu parametre `Embedding` katmanının ağırlıklarının dosyadan yüklenmesini ifâde eder. Depoda 400 bin cümle ve 100 kelîme gömme boyutu için kaydedilmiş ağırlıklar mevcuttur. Bu iki parametrenin değerini (cümle sayısı ve kelîme gömme boyutu) değiştirmedikçe, bu parametreye `False` vermeniz gerekmez. Ayrıca değiştirdiğiniz parametreler için kodu çalıştırdığınızda yeni ağırlıklar kaydedileceğinden kodu ikinci kez çalıştırırken bu parametreye `True` vererek gereksiz işlemden kaçınabilirsiniz.
  
  - `gruCellSize = 196` : Kodlayıcı (encoder) ve kod çözücü (decoder) yapılarındaki `GRU` ağlarının hücre sayısını belirtir. Güçlü bir cihazınız varsa bu parametreye 256 ve daha fazlasını verebilirsiniz.
  
  - `epochNum` : Model eğitimi için tur (epoch) sayısını belirtir. Bu tür çetrefilli modellerde tur sayısını yüksek tutmak iyi olabilir. Güçlü cihazınız varsa 30 - 50 gibi yüksek bir değer verebilirsiniz. Eğer modeli ekran kartı üzerinden değil de, CPU üzerinde eğitiyorsanız bir tur süresi yaklaşık 4 saat alabilir; ekran kartında ise bu süre 20-30 dakîkaya kadar inmektedir (varsayılan parametreler için).
  
  - `batchSizeNum = 90`  : Eğitim için yığın (batch) boyutudur. Bu model ağır ve hacimli bir model olduğundan yığın boyutunu yüksek vermeniz pek mümkün değildir.
  
  - `learningRate = 0.004` : Yinelenen sinir ağlarında başarılı bir en iyileyici olan `RMSprop` en iyileyicisinin öğrenme katsayısını ifâde eden hiperparametredir.
  
  - `loadModelWeights = True` : Eğer modeli sıfırdan eğitmeyip, mevcut kayıtlı model üzerinden eğitmek istiyorsanız, bu parametreye `True` vermelisiniz. Bir kez modeli eğittiğinizde ilgili modelin ağ ağırlıkları kod içerisinde `modelSavePath` isimli hiperparametreyle belirtilen dosya yoluna kaydedileceğinden, sonraki eğitimleriniz için bu parametreye `True` verebilirsiniz.
  
  - `trModelPath` : Türkçe dil modelinin dosya yolu
  
  - `engModelPath` : İngilizce dil modelinin dosya yolu
  
  - `dataSetPath` : Veri setinin dosya yolu
  
  - `modelSavePath` : Modelin kaydedileceği / geri yükleneceği dosya yolu
  
  - `weightsOfEmbeddingEncoderPath` : Kodlayıcının `Embedding` katmanının ağ ağırlıklarının dosya yolu (farklı cümle ve gömme boyutuna göre farklı isimle kaydedilir)
  
  - `weightsOfEmbeddingDecoderPath` : Kod çözücünün (decoder) `Embedding` katmanının ağ ağırlıklarının dosya yolu (farklı cümle ve gömme boyutuna göre farklı isimle kaydedilir)

### SİNİRSEL MAKİNE ÇEVİRİSİ HAKKINDA

- Sinirsel makine çevirisi, bir doğal dilde kurulan cümlenin başka bir doğal dile çevrilmesi işleminin sinir ağlarıyla yapılması demektir.

- Doğal diller oldukça esnek, dinamik ve çok çeşitli yapılar olduğundan doğal dilleri gramer ve istatistik kurallarıyla anlamak zordur.

- 2015'li yıllara makine çevirisi genellikle gramer ve istatistik kurallarına dayanan modellerle yapılıyordu. Bu modellerde, istatistikten sonuna kadar faydalanılmaya başlandı; fakat yine de çeviriler pek çok kez bir insân gözetiminden geçmeye muhtaçtı.

- Günümüzde ise, iyi modeller artık neredeyse hiç anlatım bozukluğu yapmıyor, kelîmeleri cümlenin bağlamına göre değerlendiriyor ve karmaşık olan cümlelerde bile yüksek performans gösteriyor; çünkü günümüzde makine çevirisi yapay sinir ağlarıyla yapılıyor.

- Yapı hakkında yalın görüntü (https://docs.pytorch.org/tutorials/_images/seq2seq.png):
  
  <img title="" src="https://docs.pytorch.org/tutorials/_images/seq2seq.png" alt="">

- Detaylı görüntü (https://iq.opengenus.org/content/images/2022/08/Un-1.png) :
  
  <img title="" src="https://iq.opengenus.org/content/images/2022/08/Un-1.png" alt="">

### VERİ SETLERİ

- **Veri seti** : Çeviri için akla gelen ilk veri seti kaynaklarından birisi tatoeba.org adresidir. Buradaki nisbeten kısa cümleler; fakat şu an mükemmel bir sistem tasarlamaya çalışmadığımız için ve veri büyüklüğü iyi seviyede olduğu için kullanılabilir. Veri seti, https://tatoeba.org/tr/downloads adresinden indirilmiştir. Websitesinin anasayfasının adresi https://tatoeba.org/tr 'dir. Cümleler CC-BY lisansı ve bu lisansın farklı versiyonları altında sunulmaktadır.

- **İngilizce kelîme vektörü veri seti** : Stanford Üniversitesi'nin hâzırladığı GLoVe kelîme vektör modeli (https://github.com/stanfordnlp/GloVe?tab=readme-ov-file), kamu malı tahsis lisansı (Open Data Commons Public Domain Dedication and License (PDDL)) altında sunulmaktadır.

- **Türkçe kelîme vektörü veri seti** : MIT lisansıyla sunulan, eğitilmiş bir Word2Vec kelîme vektör modeli (https://github.com/akoksal/Turkish-Word2Vec?tab=readme-ov-file)

### ÖN İŞLEME ve MÎMARİ YAPI

#### Veriler ve Ön İşleme

- Sinirsel makine çevirisinde veriler cümlelerdir; her kaynak cümlenin bir de hedef dildeki hâli olmalıdır.

- Verilerin makine çevirisi modeline verilmeden önce geçirilmesi gereken pek çok işlem var ve bu işlemler bâzı noktalarda dil özelinde detaylanabiliyor. Bu kapsamda yapılan ön işlemleri ikiye ayırıyorum:
  
  1. **Veri özelindeki ön işlemeler** : Veri özelindeki ön işlemeler `VeriOnIsleme.ipynb` dosyasında yer almaktadır. Misal, İngilizce'de "is not" ifâdesi "isn't" olarak kısaltılabiliyor. Bizim veri setimizde her ikisi de ("is not", "isn't") bulunmalı ki model, iki kelîme için de birbirine yakın değerli bir ağırlık vektörü atayabilsin. Başka bir misal, modelin eğitiminin daha iyi ve hızlı olabilmesi için model eğitimi kısa cümlelerden başlamalıdır. Daha fazla işlem ve detay kod içerisinde açıklamalar mevcuttur.
  
  2. **Makine Çevirisi modeli için ön işleme ve hâzırlıklar** : Bu ön işleme ve hâzırlıklar projenin ana dosyası olan `MakineCevirisi.ipynb` dosyası içerisinde yer almaktadır. Bununla ilgili bâzı detaylar için buyurunuz: 

- Veri ön işleme uygulaması her dil için ayrı ayrı yapılmalıdır:
  
  1) Öncelikle cümleler sıralıysa birbirlerinden ayrılmalı.
  
  2) Eğer üst seviye bir makine çevirisi tasarlamak istiyorsanız noktalama işâretlerinin tümünü kaldırmamalısınız. Cümlelerdeki noktalama işâretlerinin kelîmelere bitişmesi model için o kelîmeyi başka bir kelîme yaptığından bu noktalama işâretleri de kelîmelerden ayrılmalı (aralarına virgül konulmalı).
  
  3) Eğer üst seviye bir makine çevirisi tasarlamak istiyorsanız, özel isimleri küçük harfe çevirmemelisiniz; ama daha sade bir model için bunlarla uğraşmayabilirsiniz. Çünkü bu, cümle içerisindeki özel isimleri tanımayı gerektirir; bunun için de bir **isimlendirilmiş varlık tanıma (named entity recognition)** modeli tasarlamanız gerekir.
  
  4) Modelinizin cümlenin nerede başlayıp, nerede biteceğini tahmîn edebilmesi için cümle başını ve sonunu **veri setiniz içerisinde bulunmayan** özel bir karakter kümesiyle ifâde etmeniz gerekmektedir. Bunun için `<START>` ve `<STOP>` gibi bir özel işâret seçebilirsiniz; fakat `<` ve `>` karakterleri cümlelerden çıkarmak istediğimiz karakterler olduğundan cümlelerinizi bu karakterlerden temizledikten sonra bu simgeleri koymalı ve bu simgelerin kaldırılmaması için `Tokenizer` nesnenizin `filters` parametresini değiştirmelisiniz. Bunun yerine daha kolay ve daha iyi bir yöntem olarak başlangıç için `ssss` ve bitiş için `eeee` gibi karakter ataması yapabilirsiniz.
  
  5) Ardından kelîmelerini simgeleştirmelisiniz (tokenization). Bu, her kelîmeniz için bir sıra numarası (indeks) atanması anlamına gelmektedir.
  
  6) Bu işlem uygulandıktan sonra cümlelerinizi eşit uzunluğa getirmelisiniz. Cümleleriniz için bir uzunluk belirlemeli ve bu uzunluktan kısa olan cümlelerin `0` ile doldurulmasını, uzun olan cümlelerin ise kırpılmasını sağlamalısınız.

- Yukarıdaki adımlar her iki dilin verisi için uygulandıktan sonra model oluşturulmaya başlanabilir.

##### Modeli Tanıyalım

- Sinirsel makine çevirisi modeli temelde iki bileşenden oluşur:
  
  1. **Encoder (kodlayıcı)** : Bu, birkaç katmanın birbirine bağlandığı bir derin öğrenme modelidir. Bu modelin bir çıktısı olmaktadır; buna **düşünce vektörü** veyâ '**thought matrix**' denir. Bu, cümlenin anlamını ifâde eden bir vektördür. Bu modele vektör hâline getirilmiş cümle verilir.
  
  2. **Decoder (kod çözücü)** : Kodlayıcıdan biraz daha karmaşıktır. Kod çözücü kodlayıcıdan aldığı sâbit uzunluklu düşünce vektörü ile o anki cümlenin hedef dildeki çevrimini ifâde eden vektörü girdi olarak alır. Burada ince bir ayrıntı vardır; cümle kod çözücünün ağın başındaki 'embedding' (gömücü, andıçlayıcı) katmanına girdikten sonra yinelemeli katmana gelirken, düşünce vektörü ise doğrudan yinelemeli sinir ağına verilmektedir; çünkü, düşünce vektörü cümlenin anlamını ifâde etmek üzere kodlanmaktadır. Kod çözücüdeki her yinelemeli sinir ağı katmanına düşünce vektörü doğrudan verilir. **Kodlayıcının aksine kod çözücüde yinelemeli sinir ağı katmanlarının sonuncusu son zamân adımının değil, her zamân adımının çıktısını vermelidir.** Çünkü burada cümlenin anlamını değil, kelîmelerini üretmeye çalışıyoruz. Son yinelemeli sinir ağı katmanından sonra en sona simge (token) sayısı adedince sinir hücresi bulunan bir tam bağlı sinir ağı katmanı eklenir. Bu katman bize her adımda hangi kelîmenin seçileceği sonucunu çıkaran bir katmandır ve kod çözücünün son katmanıdır. Eğer kayıp fonksiyonunda "**softmax**" işlemi uygulanmıyorsa (birazdan değinelecek inşâAllâh) bu katmanın aktivasyon fonksiyonu "**softmax**" olmalıdır. "softmax" her sınıf için bir olasılık değerinin atandığı bir fonksiyondur. Buradaki işlevi hangi adımda hangi kelîmenin üretileceğine dâir bir olasılık değeri vermesidir.

- Bu iki modelin birleştirilerek uçtan uca eğitilmesine "diziden diziye" anlamına gelen "**sequence to sequence (seq2seq)**" ismi verilmektedir.

- Kodlayıcı ve kod çözücü modeller bir **embedding** (andıçlayıcı, gömücü) katmanıyla başlamalıdır. Bu sayede model eğitildikçe kelîmelerin ağırlıkları da eğitilir.

- Bu bütünleşik modelin kayıp fonksiyonu "**sparse categorical cross entropy**" olmalıdır. Bu kayıp fonksiyonu verilen indis numarasıyla, çıktının ilgili indisteki değerinin diğer indisteki değerlerden yüksek olması durumunda doğru sonucunu üreten bir kayıp fonksiyonudur. Makine çevirisinde kelîme sayısı çok fazla olduğundan kelîmeleri "tek nokta vektörü (one hot vector)" olarak temsil etmek imkansızdır. Bu sebeple kayıp fonksiyonu "categorical cross entropy" yerine "sparse categorical cross entropy" olarak seçilmelidir.

- Bu eğitim modelimizdir. Kodlayıcı ve kod çözücü modelleri aynı ağ katmanlarını kullanan farklı modeller olarak hâzırlamamız gerekmektedir.

- Eğitimdekinin aksine çeviri yaparken kod çözücü modele gerçek cümleleri vermemeliyiz; fakat model bizden bu girdiyi beklemektedir. Bu girdi için en başta cümle başlangıç simgesini (token) vermeli ve kelîmeler üretildikçe üretilen kelîmeyi modele vermeliyiz.

- Model mimarisi:
  
  <img title="" src="https://github.com/369553/MakineCevirisi/blob/main/modelTrain.png?raw=true" alt="">

- ..

##### Teknik Yapılar

- Bu projede Keras - TF yapıları ve Word2Vec dil modeli kullanılmıştır.

- Cümlelerin simgeleştirilmesi (tokenization) : `Tokenizer` (`tensorflow.keras.preprocessing.text` kitâplığı altında)

- Kelîmelerin vektörleştirilmesi (embedding) : `Embedding` (`keras.layers` kitâplığı altında)

- En gelişmiş yinelemeli sinir ağlarından olan GRU : `GRU` (`keras.layers` kitâplığı altında)

- Kelîmelerin `Embedding` katmanı ağırlıklarına atanmak üzere alınan kelîme vektörleri hâzır eğitilmiş açık kaynaklı `gensim` kütüphânesiyle sunulan Word2Vec ve GLoVe modelleridir.

##### Püf Noktalar

- Eğitim ve kullanım için biraz farklı yapıda modellere ihtiyacınız olduğundan modelinizi hâzırlarken fonksiyonel API'ı kullanın.  

- Kodlayıcı (encoder) ve kod çözücü (decoder) modellerinizin ilk katmanı olan `Embedding` katmanı kelîme adedince yapay sinir hücresi barındıran bir ağ katmanıdır. Bu katman kelîmelerin ağırlıklarını rastgele biçimde başlatır. Makine çevirisi modeli uzun ve mâliyetli bir eğitim süreci istediğinden bu `Embedding` katmanındaki ağ ağırlıklarını rastgele olarak değil, bir dil modelindeki ilgili kelîmelerin ağırlıkları olarak başlatmak sonuca daha hızlı yakınsamanızı ve modelin cümle anlamını daha iyi modellemesini sağlar. Bu sebeple en azından kodlayıcının `Embedding` katmanının ağ ağırlıklarını bir dil modelinden almak iyi bir fikirdir.

- Kelîmeleriniz temsil boyutunu 100'den aşağı yapmayın.

- 100 bin veyâ 200 bin cümle bile model için azdır. Mümkünse modeliniz daha fazla veriyle eğitin (cümle sayısı arttıkça tekil (münferid) kelîme sayısı artacağından kodlayıcı ve kod çözücüdeki `Embedding` katmanlarının sinir hücresi sayısı ve kod çözücünün son katmanı olan `Dense` katmanının sinir hücresi sayısı da artacaktır) 

- Makine çevirisi yüksek miktarda bellek ve eğitim süreci gerektirmektedir.

- Kodlayıcıya verileriniz verirken ters sırada vermeniz ve doldurma ("padding") işlemini cümle başına uygulanacak şekilde yapmanız; kod çözücüde ise bunun tam tersini yapmanız modelinizin bağlamı daha iyi yakalaması ve çeviri işlemini daha iyi yapması için bir yardımcı yöntemdir. Bu yöntemle kod çözücü model cümlenin başındaki kelîmeyi en son görmekte ve kodlayıcı model bu kelîmeyi ilk kelîme olarak daha iyi üretmektedir. Bu, uzun cümlelerde farkını daha çok hissettirebilecek bir püf noktadır. Bu yöntemle ayrıca, kırpılan cümlelerinizdeki anlam kaybı azalmaktadır; çünkü genelde cümle başı cümle sonuna göre daha önemlidir.

# 
