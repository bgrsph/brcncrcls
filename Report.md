
# Rapor: Two-Stage Convolutional Neural Network for Breast Cancer Histology Image Classification

## Genel Bakış

Öncelikle bu projedeki amaç mikroskoptan alınan göğüs kanseri histoloji taramalarını 4 sınıftan oluşan bir sınıflandırmaya almak. Bu sınıflar ise normal hücre, yumuşak huylu tümör, karsinoma in situ, invasiv karsinomudur. 

https://github.com/ImagingLab/ICIAR2018

## Metod

Sistem iki tane evrişimli sinirsel ağdan(CNN) oluşmaktadır. Bunun amacı ise yüksek çözünürlükteki(2048x1536) histoloji taramalarını bir CNN'e doğrudan verilmesinin istenmeyen iki yönü var: gerçekçi olmayan hafıza ihtiyacı ve sinirsel ağın sadece karakteristik özelliklere odaklanıp kalan herşeyi egarde etme eğilimi. 

İlk olarak verilen mikroskop görüntüsü "k x k" boyutunda (parametre) ve adım sayısı(stride) "s" olan bir pencerenin(patch) kaydırılması ile pencerelere bölünmektedir. Bu metod 
pencerelerdeki ortak piksellerin çakışmasına neden olmaktadır. 

![title](overlap.png)

Bu durum başta problem gibi gözüksede, birinci sinirsel ağın pencereler arasında
olan ortak özellikleri(feature) öğrenmesini sağlamaktadır. 
Yani birinci sinirsel ağ girdi olarak bu pencereleri almaktadır. Resimlerin etiketleri(label) olmasına rağmen bu pencerelerin kendilerine ait etiketleri yoktur ancak kayıp(loss) pencerenin ait olduğu taramanın etiketine bağlı olarak kategorik çapraz entropi(categorical cross-entropy) kayıp fonksiyonu ile hesaplanmaktadır. Sonuç olarak ilk sinirsel ağ penceler arasındaki en belirgin özellikleri çıkarmaktadır. 

İlk sinirsel ağ sonuç olarak klasifike edilmiş sınıfları çıkarmak yerine son
evrişimli katı (last convolution layer) kullanarak Kanal(Channel) x 64 x 64 
boyutunda özellik haritaları (feature maps) çıkarmaktadır. Kanal sayısı ise bu 
haritaların derinliğini temsil eden bir hiper-parametredir. Özet olarak ilk sinirsel ağ resimden çakışan penceleri girdi olarak aldıktan sonra birbiriyle çakışmayan penceleri öğrenip bu pencelerden özellik haritalarını çıkarmaktadır. 

İkinci sinirsel ağın girdisi ise ilk ağın sonucu olan özellik haritalarının bir
araya gelerek oluşturduğu 3 boyutlu bir yapıdır. Bu ağ ise ilk ağ ile aynı kayıp fonksiyonunu kullanarak resimleri iki durumu öğrenerek sınıflandırıyor: çakışan pencelerin paylaştığı lokal özellikler ve çakışmayan pencerelerin paylaştığı global özellikler. 

![title](network.png)

## Uygulama

GPU yetersizliğinden dolayı eğitilen(train) edilen veriler test edilememiştir. Eğitim edilmesi için tek seferde hafızaya alınan resim sayısı yani grup(batch) sayısı azaltılmıştır. Eğitim sırasında ise azaltılmış sayıdaki verilere ve küçük epok sayısına rağmen yüzde 92'ye kadar doğruluk oranı saptanmıştır. 

DeepSlide adlı çalışma ise içeriğinden ötürü yapılmamıştır. Bu çalışmaya benzer şekilde histolojilerden yola çıkarak akciğer hücrelerindeki invasiv karsinoma hücrelerini beş alt sınıfa ayırmaktadır. Bu sınbeş sınıfı klasifike etmek için öncelikle hücreler dört alt sınıfa ayrılmalıdır. 
