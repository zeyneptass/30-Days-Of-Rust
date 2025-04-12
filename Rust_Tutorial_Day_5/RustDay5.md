# Rust Gün 5 : 

- Önceki dersimizde fonksiyonlar ve kapsam (scope) yönetimine değindik.
- Bugün Rust’ta  stack ve heap kavramları arasındaki fark ile `String` ve `str` türleri arasındaki farka değineceğim.
- Rust'ın en önemli özelliklerinden biri, bellek güvenliğini derleme zamanında garanti etmesidir. Bunun için de ownership ve stack-heap konseptlerini kullanır.
- Ownership (sahiplik) konseptine bir sonraki derste değineceğim.

# Stack ve Heap Arasındaki Fark (Stack vs Heap)

- Programlar çalışırken verileri depolamak için genellikle iki ana bellek bölgesini kullanır: Stack ve Heap.
- Çoğu programlama dili stack ve heap kavramı üzerinde çok düşünmeyi gerektirmez. Ancak Rust gibi **düşük seviyeli bellek yönetimi** sunan bir sistem programlama dilinde verilerin belleğin hangi parçasında tutulduğu önemlidir.
    
    ## Stack (Yığın) :
    
    ### Stack Nedir?
    
    - Küçük, sabit boyutlu verilerin saklandığı bellek alanıdır.
    - Çok düzenli ve hızlıdır.
    - Stack'te depolanan verilerin boyutu *derleme zamanında* bilinmelidir ve boyut sabit olmalıdır.
    
    ### Stack Nasıl Çalışır?
    
    - Veriler üst üste eklenir ve son giren ilk çıkar (LIFO - Last-In, First-Out prensibi).
    - Bu kavramı somutlaştırmak için bir tabak yığınını düşünün. Daha fazla tabak eklediğinizde, bunları yığının üstüne koyarsınız ve bir tabağa ihtiyacınız olduğunda, üstten bir tabak çıkarırsınız. Ortadan veya alttan tabak eklemek veya çıkarma işlemini yapmazsınız.
    - **Push (Ekleme):** Veriler en üste eklenir (örneğin fonksiyon çağrıldığında).
    - **Pop (Çıkarma):** Veriler en üstten kaldırılır (örneğin fonksiyon sona erdiğinde).
    - Veri ekleme (push) ve çıkarma (pop) işlemleri çok hızlıdır çünkü sadece yığının en üstündeki işaretçiyi hareket ettirmek yeterlidir.
    
    ### Stack Pointer :
    
    - Stack pointer, işlemcinin bellekte stack’in neresine veri yazılacağını ve nereden veri alınacağını gösteren özel bir kayıttır.
    - Bir değişken oluşturulduğunda, stack pointer aşağıya kayar ve o değişkenin bellekteki yeri belirlenir.
    
    ### **Stack’in Kullanıldığı Yerler :**
    
    - Stack, **derleme zamanında boyutu bilinen ve sabit olan veriler** için kullanılır.
    - **Temel (Primitive) veri türlerinde kullanılır.**
    - **Örneğin t**üm **sayısal türler `i32`**, **`u64`**, **`f32`**, **`usize`** vb.
    - **Mantıksal türler**: **`bool`** (**`true`**/**`false`**)
    - **Karakterler**: **`char`** (Unicode skaler değerleri)
        
        ```rust
        let age: i32 = 30;      
        let is_active: bool = true; 
        let letter: char = 'A';  
        ```
        
    - Boyutu derleme zamanında bilinen diziler:
        
        ```rust
        let numbers: [i32; 3] = [1, 2, 3];
        ```
        
    - Tuple’lar :
        
        ```rust
        let point: (i32, f64) = (10, 2.7); // Stack'te
        ```
        
    - Fonksiyon çağrıları ve fonksiyon içindeki yerel değişkenler (boyutu bilinen türler: i32, bool, f64, referanslar, sabit uzunlukta array'ler vb.) işaretçiler:
    - Her fonksiyon çağrıldığında bir **stack frame** oluşturulur. Yerel değişkenler, parametreler ve dönüş adresleri burada saklanır.
        
        ```rust
        fn sum(a: i32, b: i32) -> i32 { // a ve b stack'te
        	let result = a + b; // result stack'te
        	result
        }
        ```
        
    - Özyineleme (Recursion) sırasında her çağrı için yeni bir stack frame oluşur.
    - Düşük gecikmeli işlemler için idealdir (matematiksel hesaplamalar gibi).
    - Embedded (Gömülü) Sistemlerde stack, tahmin edilebilir bellek kullanımı sağlar.
    - Tüm alanları stack’te saklanabilen struct’lar:
        
        ```rust
        struct Point {
        	x: i32,
        	y: i32,
        }
        let origin = Point { x: 0, y: 0 }; // Stack'te
        ```
        
    - Fonksiyonlara geçirilen parametreler stack’e pushlanır.
        
        ```rust
        fn print_number(num: i32) { // num stack'te
        println!("{}", num);
        }
        ```
        
    
    ### Stack’te Bellek Temizleme :
    
    - Stack'teki değişkenler, tanımlandıkları kapsam (scope) bittiği an **anında bellekten kaldırılır**.
        
        *Örnek:*
        
        ```rust
        {
        	let x = 5; // Stack'e push
        	println!("{}", x);
        } // x burada otomatik silinir (pop)
        ```
        
    - Her fonksiyon çağrıldığında bir **stack frame** oluşturulur. Fonksiyon bittiğinde, tüm yerel değişkenlerle birlikte **stack frame silinir**.
        
        *Örnek :*
        
        ```rust
        fn foo() {
        	let y = 30; // Stack'e push (foo'nun stack frame'ine)
        } // y burada silinir (foo'nun stack frame'i yok edilir)
        
        fn main() {
        	foo();
        } // main'in stack frame'i de sona erer
        ```
        
    - Stack Pointer ile de temizleme işlemi yapılır. Bir değişken kapsam dışına çıktığında; stack pointer aşağı kaydırılır (memory "silinmiş" sayılır). **Fiziksel silme yoktur**, sadece yeni veriler üzerine yazılabilir. Bu işlem 0 CPU maliyeti ile gerçekleşir. Bellek ayırma/serbest bırakma için ek kod çalışmaz.
    
    ## Heap (Yığın Bellek - Öbek) :
    
    ### Heap Nedir?
    
    - Dinamik, büyük veya boyutu derleme zamanında bilinmeyen verilerin saklandığı bellek alanıdır.
    - Veri boyutu aynı zamanda derleme zamanında belirlenebilir veya değişebilir.
    - Stack’e göre daha az düzenlidir.
    - Stack'ten farklı olarak daha esnek ama daha yavaştır.
    - Dinamik yapılar (`Box`, `String`, `Vec` gibi) burada saklanır.
    
    ### **Heap Nasıl Çalışır?**
    
    - Bellekte boş bir alan bulunur ve veriler oraya yerleştirilir.
    - Bellekte uygun boyutta bir yer aramak ve bu alanı "ayırmak" (allocate) gerektiği için çalışması Stack'e göre daha yavaştır.
        
        
        **Heap’te Alan Tahsis Etme (Allocation) :**
        
        - Memory allacator (bellek tahsis edici); yığında yeterince büyük boş bir nokta bulur, kullanımda olarak işaretler ve o konumun adresi olan bir pointer (işaretçi) döndürür.
        - Pointer sabit bir boyutta olduğundan yığında saklayabiliriz ancak gerçek verileri istediğimizde pointer’ı takip etmeliyiz.
        - Bu kavramı somutlaştırmak için bir restoranda oturduğunuzu düşünün. İçeri girdiğinizde, grubunuzdaki kişi sayısını belirtirsiniz ve sunucu herkese uyan boş bir masa bulur ve sizi oraya götürür. Grubunuzdaki biri geç gelirse, sizi bulmak için nerede oturduğunuzu sorabilir.
        - Heap’te pushing işlemi, yığında tahsis etmekten daha hızlıdır çünkü tahsis eden kişi yeni verileri depolamak için bir yer aramak zorunda kalmaz. Yeni verileri yığının en üstüne konumlandırır.
        - Yani heap’te alan tahsis etme işlemi heap’te pushing işleminden daha fazla iş gerektir.
        
        **Heap’te Veriye Erişim :**
        
        - Heap'teki verilere doğrudan erişemeyiz. Veriye erişmek için bir işaretçiyi (pointer) takip etmek gerekir.
        - Veriye erişim stack’e göre daha yavaştır çünkü oraya ulaşmak için bir pointer’ı takip edip adresi çözmeyi gerektirir.
        
    
    ### **Heap Neden Yavaştır?**
    
    - Yer arama maliyeti yüksektir çünkü her ayırmada uygun boş alan aranır.
    - Pointer takibi yapıldığı için veriye ulaşmak için ekstra işlem gerekir.
    - Veriler dağınık olduğundan CPU cache verimsiz çalışır. Bu sebeple işlemci optimizasyonu sağlamak zordur. Günümüzdeki işlemciler, bellekte daha az zıplarlarsa daha hızlıdır.
    - Somutlaştırmak gerekirse; bir restoranda birçok masadan sipariş alan bir garson düşünün. Bir sonraki masaya geçmeden önce tüm siparişleri bir masadan almak en verimli yöntemdir. A masasından bir sipariş almak, ardından B masasından bir sipariş almak, ardından A'dan tekrar bir sipariş almak ve ardından B'den tekrar bir sipariş almak çok daha yavaş bir işlem olacaktır.
    
    ### **Heap’in Kullanıldığı Yerler :**
    
    - Boyutu çalışma zamanında değişebilen veya derleme zamanında boyutu bilinmeyen veriler (String, Vec<T>, Box<T> gibi dinamik koleksiyonlar ve akıllı işaretçilerle yönetilen veriler).
    - Kodunuz bir fonksiyon çağırdığında, fonksiyona geçirilen değerler (potansiyel olarak heap’teki verilere işaretçiler dahil) ve fonksiyonun yerel değişkenleri heap’e itilir. Fonksiyon sona erdiğinde, bu değerler heap’ten çıkarılır.
        
        *Örnek :* 
        
        ```rust
        fn main() {
        	// Heap'te String oluşturma
        	let s = String::from("Merhaba");
        }
        ```
        
    
    ### Heap’te Bellek Temizleme :
    
    - Kullanılmayan verilerin silinmesi gerekir.
    - Heap'teki verilerin ne zaman temizleneceği daha karmaşıktır. Diğer dillerde bu genellikle Garbage Collector (Çöp Toplayıcı) veya manuel bellek yönetimi (C/C++'daki malloc/free) ile yapılır.
    - Rust dilinde bu noktada Ownership (Sahiplik) sistemi devreye girer.
    - Kodun hangi bölümlerinin heap’teki hangi verileri kullandığını takip etmek, heap’teki yinelenen veri miktarını en aza indirmek ve heap’te kullanılmayan verileri temizleyerek alanınızın bitmemesini sağlamak, sahipliğin (ownership) ele aldığı sorunlardır.
    - Yani sahipliğin (ownership) temel amacı heap verilerini yönetmektir.
    
    Özet olarak; stack hızlıdır, LIFO düzenindedir ve derleme zamanında boyutu bilinen veriler içindir. Heap daha esnektir, çalışma zamanında boyutu belirlenen veriler içindir ancak daha yavaştır ve yönetimi daha karmaşıktır.
    

---

# `String` ve `str` Farkı :

### Temel Fark (Değiştirilebilirlik ve Bellek):

- String türünün değiştirilebilir olmasının ana nedeni, verilerini *heap*'te saklaması ve yönetmesidir. Bu, program çalışırken boyutunun büyüyüp küçülmesine ve içeriğinin güncellenmesine olanak tanır.
- Dize sabitleri (&str) ise programın bir parçası olarak derlenir, boyutları sabittir ve değiştirilmeleri amaçlanmamıştır (ve mümkün değildir).

Rust'ta metinlerle çalışmak için iki ana tür vardır:

1. String Literal **`&str`** (Dize Sabitleri) :
    - Bunlar programın koduna doğrudan yazılır, boyutları sabittir ve değiştirilemezler.
    - Bellekte verimli bir şekilde saklanırlar.
    
    *Örnek:*
    
    ```rust
       let s = "merhaba"; *// &str türü*
    ```
    
    ```rust
    // Fonksiyon parametresi olarak
    fn yazdir(mesaj: &str) {
        println!("{}", mesaj);
    }
    
    yazdir("Bu bir string literal");  // Çıktı: Bu bir string literal
    ```
    
2. String  Türü : 
    - Boyutu çalışma zamanında değişebilen metinleri saklamak için tasarlanmıştır.
    - Bellekte *heap* denilen alanda yer ayırır ve bu alanı yönetir. Bu sayede değiştirilebilir (mut anahtar kelimesi ile).
    - Ownership kuralları, `String` gibi heap verilerinin güvenli şekilde temizlenmesini sağlar.
    - Örneğin, push_str() metoduyla sonuna yeni metin eklenebilir.
    
    *Örnek:* 
    
    ```rust
    let mut s = String::from("Merhaba"); // Heap'te tahsis edilir
    s.push_str(", Dünya!"); // Değiştirilebilir
    println!("{}", s); // "Merhaba, Dünya!"
    ```
    
    ### **Rust'ta String Oluşturma Yöntemleri :**
    
    Rust'ta **`String`** oluşturmak için birkaç farklı yöntem bulunur. 
    
    1. **`String::new()` - Boş String Oluşturma :**  
        
        En temel yöntemdir, boş bir String oluşturur.
        
        Kapasite belirterek verimli şekilde kullanılabilir.
        
        *Örnek :* 
        
        ```rust
        let mut s = String::new(); // Boş bir String oluşturur
        s.push_str("Metin");
        println!("{}", s); // Çıktı: Metin
        ```
        
    
    1. **`String::from()` - Literal'den String Oluşturma :** 
        
        String literal'inden (**`&str`**) String oluşturur.
        
        Başlangıç değeriyle oluşturma imkanı sağlar.
        
        *Örnek:* 
        
        ```rust
        let s = String::from("Başlangıç değeri");
        println!("{}", s); // Çıktı: Başlangıç değeri
        ```
        
    
    1. **`to_string()` Metodu - Diğer Türlerden Dönüşüm :** 
        
        Rust’ın birçok türünde **`to_string()`** metodu bulunur. 
        
        Tür dönüşümlerinde kullanışlı ve zincirleme metod çağrılarına uygun.
        
        *Örnek:*
        
        ```rust
        // &str'den
        let s1 = "literal".to_string();
        // Sayısal türlerden
        let s2 = 42.to_string();
        // Formatlanmış String
        let s3 = format!("{} {}", "Formatted", "string").to_string();
        ```
        
    
    1. **`String::with_capacity()` - Önceden Kapasite Belirterek :** 
    Bellek tahsisini optimize etmek için kullanılır.
        
        Büyük String'ler (1KB+) için performans avantajı sağlar ve tekrarlı ekleme işlemlerinde verimlidir.
        
        *Örnek :* 
        
        ```rust
        let mut s = String::with_capacity(50); // 50 byte kapasiteli String
        s.push_str("Bu daha verimli bir yöntem");
        println!("Kapasite: {}, Uzunluk: {}", s.capacity(), s.len());
        ```
        
    
    1. **`From` Trait Implementasyonları :** 
        
        Çeşitli türlerden **`String`** oluşturmak için kullanılır.
        
        Başlangıç değeri belli olan String'ler için kullanılır.
        
        *Örnek :*
        
        ```rust
        let s1: String = From::from("From trait kullanımı"); // &str türünden String'e dönüştürüldü
        
        // Vec<u8>'den (UTF-8 geçerliyse)
        let vec = vec![104, 101, 108, 108, 111]; //  // "hello" kelimesinin UTF-8 karşılığı
        let s2 = String::from_utf8(vec).unwrap(); // Vec<u8> türünden String'e dönüştürüldü
        
        // Yukarıdaki s1,s2 ve vec türleri trait implementasyonları ile String'e dönüştürüldü. 
        ```
        
    
    1. **`format!` Makrosu - Formatlanmış String :** 
        
        Birden çok değeri birleştirerek String oluşturur.
        
        *Örnek :*
        
        ```rust
        let name = "Ahmet"; 
            let age = 30; 
            let s = format!("{} {} yaşında", name, age); // format! makrosu ile string oluşturma
            println!("{}", s); // Çıktı: Ahmet 30 yaşında
        ```
        
    
    ### **Rust'ta Metin Ekleme İşlemleri**
    
    **1. `push_str()` - String'e Metin Ekleme :** 
    
    - String'in sonuna başka bir metin ekler
    - Parametre olarak **`&str`** alır
    - O(1) zaman karmaşıklığına sahiptir (amortized)
    
    ```rust
    let mut s = String::from("Merhaba");
    s.push_str(" Dünya!"); // String'e &str ekler
    println!("{}", s); // Çıktı: "Merhaba Dünya!"
    ```
    
    - **`push_str`** ownership almaz, sadece referans kullanır:
    
    ```rust
    let mut s = String::from("foo"); 
    let ek = "bar"; 
    s.push_str(ek); // "foo" + "bar" şeklinde birleştirir
    println!("{}", ek); // Çıktı: bar "bar" hala kullanılabilir
    ```
    
    **2. `push()` - Tek Karakter Ekleme :** 
    
    - String'in sonuna tek bir karakter ekler.
    - **`char`** türünde parametre alır.
    
    ```rust
    let mut s = String::from("Lo");
    s.push('l'); // 'l' karakterini ekler
    println!("{}", s); // Çıktı: "Lol"
    ```
    
    **3. `+` Operatörü ile Birleştirme :** 
    
    - İki String'i veya String ile &str'yi birleştirir.
    - Sol operandın ownership'ini alır (move semantics)
    - Sağ operand **`&str`** olmalıdır.
    
    ```rust
    let s1 = String::from("Hello");
    let s2 = String::from(" World");
    let s3 = s1 + &s2; // s1 move edilir, s2 referans olarak kullanılır
    println!("{}", s3); // Çıktı: "Hello World"
    // println!("{}", s1); // Hata! s1 artık geçersiz
    ```
    
    1. **`format!` Makrosu ile Karmaşık Birleştirmeler :**
        - Birden fazla String'i verimli şekilde birleştirir
        - Ownership almaz
        - Okunabilir syntax sunar
        - Performans avantajı sunar çünkü `format!` genellikle `+` zincirlerinden daha verimlidir.
        
        ```rust
        let s1 = String::from("tic");
        let s2 = String::from("tac");
        let s3 = String::from("toe");
        
        let s = format!("{}-{}-{}", s1, s2, s3);
        println!("{}", s); // Çıktı: "tic-tac-toe"
        println!("{}", s1); // "tic" hala geçerli
        ```
        

1. **`extend()` - Iterator ile Ekleme :** 
    - Bir iterator'deki tüm öğeleri String'e ekler
    - **`char`** veya **`&str`** iterator'leri kabul eder

```rust
let mut s = String::from("Rust");
s.extend([' ', 'i', 's'].iter()); // Char iterator ekler
s.extend(" awesome!".chars()); // String ekler
println!("{}", s); // Çıktı: Rust is awesome!
```

1. **`insert_str()` ile Belirli Pozisyona Ekleme :**
    - String'in belirli bir pozisyonuna metin ekler
    - UTF-8 byte indeksi kullanır
    
    ```rust
    let mut s = String::from("Hello!");
    s.insert_str(5, " Rust"); // 5. indekse " Rust" ekle
    println!("{}", s); // Çıktı "Hello! Rust"
    ```
    

### String İşlemleri için Performans İpuçları :

Rust'ta String işlemlerini verimli bir şekilde yönetmek için aşağıdaki yöntemler kullanılabilir.

1. **Kapasite Önceden Ayarlama (`with_capacity`) :**
- String'in büyümesi gerektiğinde yeni bellek tahsis etmek (reallocation) maliyetlidir
- Önceden kapasite belirlemek bu ek yükü ortadan kaldırır.
    
    ```rust
    let mut s = String::with_capacity(100); // 100 byte başlangıç kapasitesi
    s.push_str("Merhaba"); // String'e "Merhaba" ekle
    println!("Kapasite: {}, Uzunluk: {}", s.capacity(), s.len()); // Çıktı : Kapasite: 100, Uzunluk: 7
    ```
    

1. **Önceden Ayarladığımız Kapasiteyi  `reserve()` Metoduyla Optimize Etme :** 
    
    ```rust
    let mut s = String::new();
    s.reserve(50); // 50 byte ek kapasite
    ```
    

1. **Iterasyon ile Çoklu Veri işlemlerinde Optimizasyon :**
- `String::new` ile `String` türünü oluşturma işlemi verimsiz bir yaklaşımdır.
    
    ```rust
    let mut s = String::new();
    	for i in 0..100 {
    		s.push_str(&i.to_string()); // Her iterasyonda reallocation riski
    }
    ```
    

- `String::with_capacity` yukarıdaki kodu optimize ederek daha verimli bir yaklaşım sağlayabiliriz.
    
    ```rust
    let mut s = String::with_capacity(100 * 3); // 100 sayı * ortalama 3 karakter
    	for i in 0..100 {
    		s.push_str(&i.to_string());
    }
    ```
    

1. **Temel `extend()` Kullanımı :** 
- **`extend()`** metodu, bir koleksiyonu **`Iterator`** ile genişletmek için kullanılır.
- String'ler için özellikle verimli bir birleştirme yöntemidir.
- **`extend()`** kapasiteyi önceden tahmin edebilir (Rust 1.54+)
    
    ```rust
    let numbers: Vec<String> = (0..100).map(|i| i.to_string()).collect(); //0'dan 100'e kadar olan sayıları string'e çevirip bir vektör oluşturuyoruz.
    let mut s = String::new(); // Boş bir string oluşturuyoruz.
    s.extend(numbers.iter().map(|x| x.as_str())); // numbers vektöründeki her bir elemanı string'e çevirip extend fonksiyonu ile s string'ine ekliyoruz.
    println!("{}", s); // Burada s string'ini ekrana yazdırıyoruz.
    ```
    

---