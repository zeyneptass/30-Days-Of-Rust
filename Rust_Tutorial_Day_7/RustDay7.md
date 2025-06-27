# Rust Gün 7 :

- Önceki gün,  `ownership`, `barrowing` ve `referans` kavramlarına ve `slice`türüne  değindim.
- Bugün ise rust programlama dilinde Struct, Struct tuple, kullanımlarına, impl method, **Associated Function ve Instance Method konularına değineceğim.**

---

# Struct

- Dördüncü gün fonksiyonlardan bahsederken `struct` tanımlayıp bunu bir fonksiyona parametre olarak vermiştim.
- Şimdi struct yapısından detaylı olarak bahsedeceğim.
- Struct, birbiriyle ilişkili verileri gruplayarak anlamlı bir bütün oluşturan özel bir veri türüdür.
- Nesne yönelimli dillerdeki nesne özelliklerine benzer şekilde, örneğin C#’ta field kullanımı gibi.
- Struct, tuple gibi birden fazla ilişkili değer tutar ancak struct'lar alanlara isim vererek daha okunabilir ve esnek bir yapı sunar.
- Tuple'larda verilere **sadece sıralı indislerle** (örn. **`tuple.0`**, **`tuple.1`**) erişilirken, struct'larda her alanın **anlamlı bir adı** vardır (örn. **`user.name`**, **`user.age`**).
- Struct'ta, veri organizasyonu tuple’a göre daha uygundur ayrıca büyük projelerde veri yönetimini kolaylaştırır.

## Struct Tanımlama :

- **`struct`** anahtar kelimesi + yapı adı kullanılır (örneğin, **`User`**).
- Süslü parantezler (**`{}`**) içinde **alan adları** ve **türleri** belirtilir.
- Bir struct’ı tanımladıktan sonra kullanmak için, her alan için somut değerler belirterek o yapının bir örneğini oluştururuz. Syntax olarak :
    
    ```rust
    let örnek_adı = StructAdı {
        alan1: değer1,
        alan2: değer2,
        // ...
    };
    ```
    
- Alanlar, struct tanımındaki sırayla aynı olmak zorunda değildir ancak tüm alanların değerleri belirtilmelidir. Örneğin, `active` ve `username` alanlarının sırası değiştirilebilir.
- Her alan için **`alan_adi: değer`** şeklinde atama yapılır.

Örnekteki **`User`** struct'ı : Türün şablonudur (alan adları ve türleri).

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

Örnek kullanımı : Şablonu gerçek verilerle doldurur (somut değerler)

```rust
let user1 = User {
    active: true,
    username: String::from("someusername123"),
    email: String::from("someone@example.com"),
    sign_in_count: 1,
};
```

### **Struct’tan Değer Elde Etmek :**

- Nokta gösterimini kullanırız.
- Örneğin, bu kullanıcının e-posta adresine erişmek için user1.email kullanırız.
- Örnek değiştirilebilirse, nokta gösterimini kullanarak ve belirli bir alana atayarak bir değeri değiştirebiliriz.
    
    ```rust
    fn main() {
        let mut user1 = User {
            active: true,
            username: String::from("someusername123"),
            email: String::from("someone@example.com"),
            sign_in_count: 1,
        };
    
        user1.email = String::from("anotheremail@example.com");
    }
    ```
    

## **Struct’ta Değiştirilebilirlik (Mutability) :**

- Rust'ta bir struct örneği **ya tamamen değiştirilebilir (mutable) ya da tamamen değiştirilemez (immutable)** olmalıdır.
- Belirli alanları seçerek değiştirilebilir yapma imkanı yoktur.
- Aşağıdaki durumda `user1` adlı değişkenin **tüm alanları** yazılabilir/düzenlenebilir olur.
- `mut` bir struct'ta istersen tek bir alanı, istersen hepsini değiştirebilirsin.
    
    ```rust
    let mut user1 = User { /* ... */ };  // Tüm örnek değiştirilebilir
    user1.email = String::from("new@email.com");  // Değişiklik yapılabilir
    ```
    

- Aşağıdaki ifade yanlıştır çünkü struct’ta **belirli alanları tek tek `mut` yapmak mümkün değildir**.
    
    ```rust
    struct User {
        mut email: String, // ❌ geçersiz
        username: String,
    }
    ```
    

## **Fonksiyondan Struct Döndürme :**

- Bir fonksiyonun son ifadesi olarak struct örneği oluşturulabilir ve bu örtük olarak döndürülür.
- Struct'lar fonksiyonlardan doğrudan döndürülebilir (return anahtar kelimesine gerek yoktur).
    
    ```rust
    fn build_user(email: String, username: String) -> User {
        User {
            active: true,
            username: username,  // Parametre değeri atanıyor
            email: email,        // Parametre değeri atanıyor
            sign_in_count: 1,
        }
    }
    ```
    

### **Field Init Kısaltmasını Kullanma :**

- Parametre isimleriyle struct alan isimleri aynı olduğunda, **"field init shorthand"** kısaltması kullanılabilir.
- Bu, tekrarlanan yazımı önler ve kodu daha okunabilir yapar.
    
    ```rust
    fn build_user(email: String, username: String) -> User {
        User {
            active: true,
            username,  // "username: username" yerine kısaltma
            email,     // "email: email" yerine kısaltma
            sign_in_count: 1,
        }
    }
    ```
    

## Struct Update Syntax ile Diğer Örneklerden Örnekler Oluşturma :

- Rust’ta "struct update syntax"  (**`..`**), özellikle bir struct’ın bazı alanlarını değiştirip, diğer alanlarını mevcut başka bir struct örneğinden almak istediğimizde kullanırız.
- **Move Semantics:** **`String`** gibi türler taşındığı için orijinal struct artık kullanılamaz (**`Copy`** trait'i yoksa).
- **`Copy` Türleri:** **`i32`**, **`u32`**, **`f64`** gibi basit türlerde orijinal struct hala geçerlidir.
- Struct update syntax (**`..`**) kullanırken **taşınma kurallarını** her zaman göz önünde bulundurmalıyız.

**Struct Update Syntax Kullanım Alanları:**

- Varsayılan ayarları kopyalayıp birkaç alanı değiştirmek.
- Fonksiyonlarda yapıları kısmen güncellemek.
- Büyük struct'larda manuel kopyalamayı önlemek.

Temel Kullanım :

- En temel kullanım senaryosunda, mevcut bir struct örneğinin bir veya birkaç alanını değiştirerek yeni bir örnek oluştururuz. Bu sırada, Copy trait'ine sahip olmayan alanlar (örneğin String) yeni struct'a **taşınır** ve orijinal struct'ın bu alanlara erişimi kaybolur.
- Aşağıdaki örnekte, person1 örneğinin değerleri kullanılarak name alanı farklı olan yeni bir person2 örneği oluşturulmaktadır.
    
    ```rust
    #[derive(Debug)]
    struct Person {
        name: String,
        age: u32,
        city: String,
    }
    
    fn main() {
        let person1 = Person {
            name: String::from("Ahmet"),
            age: 30,
            city: String::from("İstanbul"),
        };
    
        // `person1`'den `age` ve `city`'yi al, sadece `name`'i değiştir.
        let person2 = Person {
            name: String::from("Mehmet"),
            ..person1
        };
    
       println!("{:?}", person2); // Person { name: "Mehmet", age: 30, city: "İstanbul" }
    }
    ```
    
- Yukarıdaki örnekte, **`person2`**, **`person1`**'in **`age`** ve **`city`** değerlerini alırken, **`name`** değiştirildi.

***💡 Not:* `person1.age`** gibi **`Copy`** türündeki alanlar hâlâ erişilebilir fakat string türü olduğu için **`person1.city`** artık kullanılmaz.

**Tamamı Copy Türü olan Struct ile Kullanım :**

- Eğer bir struct, Copy trait'ini uygularsa (ve tüm alanları Copy türündeyse), struct update syntax'ı veri taşıma (move) yerine kopyalama (copy) yapar. Bu durumda, orijinal struct örneği tüm alanlar için kullanılabilir.
- Point struct'ı ile bu davranışı inceleyelim :

```rust
#[derive(Debug, Clone, Copy)]
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let point1 = Point { x: 10, y: 20 };
    
    // `Copy` özelliği olduğu için `point1` hala kullanılabilir.
    let point2 = Point {
        x: 5,
        ..point1
    };

    println!("point2: {:?}", point2); // Point { x: 5, y: 20 }
    println!("point1: {:?}", point1); // Point { x: 10, y: 20 } (hala geçerli)
}
```

• **`Point`** türü **`Copy`** trait'ini uyguladığı için **`point1`**'den değerler kopyalanır, taşınmaz.
• **`point1`** tüm alanlar için hala kullanılabilir.

**Partial Update (Kısmi Güncelleme) ile Varsayılan Değerler :**

- Struct update syntax, özellikle varsayılan (default) değerlere sahip yapılandırma (config) struct'ları için çok kullanışlıdır.
- Varsayılan bir yapılandırma oluşturup, yalnızca özelleştirmek istediğimiz alanları belirterek yeni bir yapılandırma türetebiliriz.
- Bu, kod tekrarını önler ve okunabilirliği artırır.

```rust
#[derive(Debug)]
struct Config {
    timeout: u32,
    retries: u32,
    log_level: String,
}

fn main() {
    let default_config = Config {
        timeout: 30,
        retries: 3,
        log_level: String::from("info"),
    };

    // Sadece `timeout` ve `log_level` değiştir, `retries`'i varsayılandan al.
    let custom_config = Config {
        timeout: 60,
        log_level: String::from("debug"),
        ..default_config
    };

    println!("{:?}", custom_config);
    // Config { timeout: 60, retries: 3, log_level: "debug" }
}
```

- **`default_config`**'tan sadece **`retries`** alınırken, diğer alanlar elle güncellendi.
- **`default_config`** struct’ı **`log_level`** alanı için artık kullanılamaz çünkü **`log_level`** alanı bir **`String`** türüdür ve taşınır (move semantics). Kopyalamaya çalışırsak aşağıdaki hatayı alırız :
    
    ```
    error[E0382]: borrow of moved value: `default_config`
    ```
    

**Fonksiyon ile Birlikte Kullanım :**

- Bu syntax, bir struct'ı parametre olarak alıp onun üzerinde küçük değişiklikler yaparak yeni bir struct döndüren fonksiyonlarda oldukça yaygın kullanılır.
- Bu, mevcut bir nesnenin bir varyasyonunu oluşturmak için temiz bir yol sunar.
- Aşağıdaki örnekte, herhangi bir Rectangle'ı alıp onu kırmızı bir Rectangle'a dönüştüren bir fonksiyon görüyorsunuz.

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
    color: String,
}

fn create_red_rectangle(base: Rectangle) -> Rectangle {
    Rectangle {
        color: String::from("red"),  // Sadece `color` değiştirildi.
        ..base                       // Diğer alanlar `base`'den alındı.
    }
}

fn main() {
    let rect1 = Rectangle {
        width: 100,
        height: 50,
        color: String::from("blue"),
    };

    let red_rect = create_red_rectangle(rect1); // Burada move semantics uygulanır.
    println!("{:?}", red_rect); // Rectangle { width: 100, height: 50, color: "red" }
}
```

- Fonksiyon içinde **`..base`** kullanarak mevcut bir yapıyı kopyalayıp sadece **`color`** değiştirildi.
- `main` fonksiyonundaki `rect1`, `create_red_rectangle(rect1)` çağrısıyla sahipliğini `base`'e devrediyor (move semantics).
- `Rectangle` yapısındaki `String` gibi alanlar **`Copy`** özelliğine sahip olmadığından, sahiplik devri sonrası `rect1` artık kullanılamaz hale gelir.
- Fonksiyon, base struct'ının sahipliğini aldığı için rect1 da create_red_rectangle çağrısından sonra kullanılamaz hale gelir.
- Yani aşağıdaki kullanım hata döndürür.

```rust
println!("{:?}", rect1); //
```

- Derleyici :

```
error[E0382]: borrow of moved value: `rect1`
```

## **Tuple Struct'lar (Demet Yapıları) :**

- Tuple struct'lar, adlandırılmış alanları olmayan, sadece tipleri belirtilmiş yapılardır.
- Normal struct'lardan farkı, alanların isimleri yerine pozisyonel indekslerle (`0`, `1`, vb.) erişilmeleridir.

**Ana kullanım amacı:**

- **Tür güvenliği** sağlamak (örneğin, **Metre** ve **Mil** aynı veri tipinde olsa bile karıştırılmaz).
- Basit gruplamalar yapmak (örneğin, **Color** için RGB değerleri).

 **Temel Tuple Struct Tanımı ve Kullanımı :** 

- Bu en temel kullanım, birbiriyle ilişkili verileri (RGB değerleri veya 2D koordinatlar gibi) tek bir tür altında toplamak için tuple struct'ların nasıl tanımlandığını ve alanlarına pozisyonel indekslerle (.0, .1) nasıl erişildiğini gösterir.
- Alan isimlerine ihtiyaç duyulmadığında pratik bir çözüm sunar.

```rust
// Tuple struct tanımları
struct Color(u8, u8, u8);       // RGB renk değerleri
struct Point(i32, i32);         // 2D koordinat

fn main() {
    let black = Color(0, 0, 0); // Siyah renk
    let origin = Point(0, 0);   // Orijin noktası

    println!("Siyah: ({}, {}, {})", black.0, black.1, black.2); // Siyah: (0, 0, 0)
    println!("Orijin: ({}, {})", origin.0, origin.1);           // Orijin: (0, 0)
}
```

- **`Color` ve `Point` farklı türlerdir**, ikisi de **`(i32, i32)`** gibi görünse bile birbirlerinin yerine kullanılamaz.
- Alanlara **`.0`, `.1`** gibi indekslerle erişilir.

### **Tür Güvenliği Sağlamada Tuple Struct Kullanımı :**

- Tuple struct'ların en güçlü yönlerinden biri, aynı temel veri tipini (örneğin f64) sarmalayarak yeni ve birbirinden farklı türler oluşturmaktır.
- Aşağıdaki örnekte, Metre ve Mil türleri, derleyicinin bu iki birimi karıştırmasını engelleyerek programda mantıksal hataların önüne geçer.

```rust
struct Metre(f64);
struct Mil(f64);

fn mesafe_hesapla(metre: Metre) {
    println!("Mesafe: {} metre", metre.0);
}

fn main() {
    let ev_uzaklık = Metre(500.0);
    let okul_uzaklık = Mil(2.0);

    mesafe_hesapla(ev_uzaklık); // Doğru
    // mesafe_hesapla(okul_uzaklık); // HATA! Tür uyuşmazlığı
}
```

- **`Metre`** ve **`Mil`** aynı **`f64`** tipinde olsa bile birbirine atanamaz çünkü tuple struct’lar Rust’ta farklı türler olarak değerlendirilir.
- Yanlışlıkla birim karışımlarını önler. Kod içinde biri **`Metre`** , diğeri **`Mil`** olan iki değişkenin yerini yanlışlıkla değiştirsen bile **derleyici hata verir**. Tip güvenliği (type safety)sağlanır.
- Böylece **mantıksal hataların önüne geçilir.**
- Bu kullanım, **aynı türdeki değerleri farklı anlamlarla** kullanmak için idealdir.

### **Pattern Matching ile Ayrıştırma :**

- Tuple struct'lar, yapıları gereği match ifadeleri ile kolayca ayrıştırılabilir.
- Bu örnekte, bir RGB struct'ının içindeki değerler, match blokları içinde kolayca ayrıştırılarak belirli renklerle eşleştirilir.
- Bu, karmaşık koşul ifadeleri yerine daha okunabilir bir kod yazmayı sağlar.

```rust
struct RGB(u8, u8, u8);

fn renk_analiz(renk: RGB) {
    match renk {
        RGB(255, 0, 0) => println!("Kırmızı"),
        RGB(0, 255, 0) => println!("Yeşil"),
        RGB(r, g, b) => println!("RGB: {}, {}, {}", r, g, b),
    }
}

fn main() {
    let yesil = RGB(0, 255, 0);
    renk_analiz(yesil); // "Yeşil"
}
```

### **Basit Türlere Anlam Kazandırma (Newtype Pattern) :**

- Basit türlere anlam kazandırmak ve tip güvenliğini artırmak amacıyla kullanılan bir programlama desenidir.
- Bu desen, temel veri türlerini (örneğin `u32`, `String`, `f32`) bir **tuple struct** ile sarmalayarak, bu türe özel bir anlam yüklemeni sağlar.
- Rust, bu yeni yapıyı tamamen **ayrı bir tür** olarak görür.
- Böylece farklı anlamlara gelen ama aynı temel tipe sahip verilerin karışmasını **derleme zamanında** önleyebilirsin.

**Kullanım Şekli :**

```rust
struct UserId(u32);     
struct OrderId(u32);    
```

- Yukarıda `UserId` ve `OrderId` ikisi de `u32` türünü sarmalıyor ama **farklı anlamlara sahip türler**.

**Kullanım Amacı :**

- **Anlam kazandırmak**: `u32` yerine `UserId`, `Kilogram`, `Meter` gibi daha anlamlı türler tanımlarsın.
- **Tip güvenliği**: Aynı temel tip olsa bile yanlışlıkla birbirine karıştırmazsın.
- **Kod okunabilirliğini artırır**: Bir fonksiyon `u32` mü alıyor yoksa `UserId` mi? Açıkça anlaşılır.

> ***💡 Not:*  Eğer `struct` içinde sadece bir alan varsa ve bu alan bir temel türse, bu desen otomatik olarak Newtype Pattern sayılır.**
> 

***Örnek :***  Aşağıdaki örnekler, `String`'e anlam kazandırır: biri bir e-posta adresini, diğeri bir kullanıcı adını temsil eder.

```rust
struct Email(String);
struct Username(String);
```

**"Newtype" Deseni ile ID'leri Ayırma :**

- Hem KullaniciID hem de UrunID aslında birer sayı (u64) olabilir.
- Ama yanlışlıkla bir kullanıcı ID'si bekleyen fonksiyona ürün ID'si göndermek istemeyiz.
- Bu durumda aşağıdaki örnekteki gibi u64 türünü **`KullaniciID`** ve **`UrunID`** ile sarmaladığımızda tip güvenliği sağlanmış olur.

```rust
struct KullaniciID(u64);
struct UrunID(u64);

fn kullaniciyi_sil(id: KullaniciID) {
    // Bu fonksiyon SADECE KullaniciID kabul eder.
    println!("Kullanıcı #{} siliniyor...", id.0);
}

fn main() {
    let kullanici_id = KullaniciID(12345);
    let urun_id = UrunID(12345);

    kullaniciyi_sil(kullanici_id); // Bu çalışır.

    // Aşağıdaki satır derleme hatası verir! Çünkü tipler farklı.
    // kullaniciyi_sil(urun_id);
    // HATA: expected struct `KullaniciID`, found struct `UrunID`

}
```

### **Birim Benzeri Yapılar (Unit-Like Structs) :**

- İçinde **hiçbir alan bulunmayan** yapılardır.
- Sadece bir isimden ibarettirler. Adını, hiçbir değeri olmayan özel bir tip olan Unit tuple’dan alırlar. Gün 2’de unit tuple’dan bahsetmiştim.

**Neden Unit Tuple Kullanırız?**

- Veri taşımaya değil, bir **durumu**, **özelliği** veya **işareti** temsil etmeye yararlar. En yaygın kullanım alanı, daha sonra değineceğim **trait'ler (özellikler)** ile birleştiğindedir.
- Bir şeye veri eklemeden ona bir "etiket" yapıştırmak veya bir "davranış" kazandırmak istediğinizde kullanılırlar.

*Örnek :* 

```rust
// Sadece bir marker (işaretleyici) olarak kullanılan struct
struct Marker;

// Hiçbir veri taşımayan bir tür
struct Empty;

// Bir olayın gerçekleştiğini belirtmek için
struct EventOccurred;

fn main() {
    let marker = Marker;
    let empty = Empty;
    let event = EventOccurred;
}
```

## **Struct’a Metot Ekleyerek Davranış Kazandırma :**

- Bir veri yapısıyla ilgili bir işlemi, o veri yapısından bağımsız bir fonksiyon olarak yazmak yerine, doğrudan o veri yapısının bir parçası, bir "yeteneği" haline getirebiliriz. Bu "yeteneklere" ***metot*** denir.
- Bu dönüşüm, kodu çok daha **organize**, **okunur** ve **nesne yönelimli** hale getirir.

### **Fonksiyondan Metoda Geçiş: impl Bloğu :**

Dördüncü gün, programı modüler hale getirmek ve belirli görevleri yerine getirmek için fonksiyonları kullandığımızı belirtmiştim ve örneklerle açıklamıştım.

Aşağıda, bir dikdörtgenin alanını hesaplamak için basit bir fonksiyon yapısı kullanılmıştır. Örnekte:

- **`Rectangle`** struct'ı tanımlanmıştır,
- **`Rectangle`**'dan **bağımsız** bir **`area`** fonksiyonu yazılmıştır,
- Tüm fonksiyonların çağrıldığı **`main`** fonksiyonu bulunmaktadır.

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

// Rectangle'dan BAĞIMSIZ bir fonksiyon
fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    // Çağırırken, Rectangle örneğini argüman olarak veririz.
    println!("Dikdörtgenin alanı: {}", area(&rect1));
}
```

- Yukarıdaki kodu metoda dönüştürerek yeniden oluşturabiliriz. Fonksiyonu `**impl Rectangle`** bloğunun içine taşıyarak onu Rectangle'ın bir metodu haline getiririz.

```rust
struct Rectangle {
    width: u32,
    height: u32,
}

// Rectangle için bir "uygulama" (implementation) bloğu
impl Rectangle {
    // Artık bu bir metot. `rectangle: &Rectangle` yerine `&self` kullanır.
    fn area(&self) -> u32 {
        self.width * self.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    // Metot sözdizimi kullanılır: instance.method()
    println!("Dikdörtgenin alanı: {}", rect1.area());
}
```

> ***💡 Not:**  Fonksiyondan farklı olarak kodu çalıştırmak için impl metotlarda; **`area(&rect1)`** yerine **`rect1.area()`** yazılır.*
> 

### Self Keyword :

**`self`**, bir metodun çağrıldığı **örneğin (instance) kendisini** temsil eden özel bir anahtar kelimedir.

- **Örnek:** **`rect1.area()`** çağrıldığında, **`area`** metodunun içindeki **`self`**, **`rect1`**'in ta kendisidir.
- **`Self` vs `self`:**
    - **`impl`** bloğu içinde **`Self`** (büyük harfle), o yapının türünü temsil eder.
    - **`self`** (küçük harfle) ise, o anki örneği ifade eder.
    - ***Örneğin:***
        
        ```rust
        impl Rectangle {
            fn area(&self) -> u32 {  // &self, &Rectangle ile aynıdır!
                self.width * self.height
            }
        }
        ```
        
    - Rust, kolaylık olsun diye **`self: &Self`** yerine kısaca **`&self`** yazmamıza izin verir.
    

**`&self`, `&mut self` ve `self` Arasındaki Farklar** 👇🏻

1. **&self** 
    - Sadece veri okumak için kullanılır.
    - Orijinal veriyi **değiştirmez**
    - **Değişmez ödünç alır.** (Immutable Borrow)
    - En yaygın kullanılan yöntem
    
    ```rust
    impl Rectangle {
        fn width(&self) -> u32 {
            self.width  // Sadece değeri okuyoruz, değiştirmiyoruz.
        }
    }
    ```
    
2. **&mut self**
    - Veriyi değiştirmek için kullanılır.
    - Orijinal veriyi **modifiye eder.**
    - **Değişken ödünç alır.** (Mutable Borrow)
    
    ```rust
    impl Rectangle {
        fn double_width(&mut self) {
            self.width *= 2;  // Genişliği ikiye katlıyoruz!
        }
    }
    ```
    

1. **self**
    - Metot çağrıldıktan sonra orijinal örnek kullanılamaz.
    - Yapının **sahipliğini alır.** (Ownership)
    - Genellikle dönüşümlerde veya kaynak temizlemede kullanılır.
    - Yapıyı tüketip başka bir şeye dönüştürür. Bu metottan sonra orijinal örnek **kullanılamaz**.
    
    ```rust
    impl Rectangle {
        fn into_tuple(self) -> (u32, u32) {
            (self.width, self.height)  // Artık `rect1` kullanılamaz!
        }
    }
    ```
    

***Örnek :**  Aşağıdaki kodda üçü içinde genel bir kullanım yer alır.*

```rust
// Doğru kullanım örneği
let mut user = User { name: String::from("Ali"), age: 30 };
user.birthday(); // &mut self gerektirir
let name = user.get_name(); // &self yeterli
let json = user.to_json(); // self alır, user artık kullanılamaz
```

### **Associated Function (İlişkili Fonksiyon) :**

- Associated Function, Rust'ta bir **`impl`** bloğu içinde tanımlanan, ancak **bir örnek (instance) üzerinde çağrılmayan** özel fonksiyonlardır.
- Bunlar, bir veri yapısıyla (struct veya enum) ilişkilendirilmiş, ancak **`self`** parametresi almayan fonksiyonlardır.
    
    **Associated Function'ın Özellikleri**
    
    1. **`self` parametresi almaz** → Bir örneğe ihtiyaç duymaz.
    2. **`::` syntax'ı ile çağrılır** (örn. **`String::new()`**).
    3. Genellikle:
        - Yapıcı fonksiyonlar (**`new`**, **`default`**) olarak kullanılır.
        - Yardımcı fonksiyonlar (factory metotları) olarak kullanılır.

### **Rust'ta Instance Method (Örnek Metodu) :**

- Instance Method, bir yapı (**`struct`**) veya numaralandırma (**`enum`**) örneği üzerinde çalışan ve **`self`** parametresi alan fonksiyonlardır.
- Bu metodlar, belirli bir veri örneğine özgü işlemleri gerçekleştirir.
    
    
    **Instance Method'un Temel Özellikleri**
    
    1. **`self` parametresi alır** (**`&self`**, **`&mut self`** veya **`self`**).
    2. **Nokta (`.`) syntax'ı ile çağrılır** (örn. **`ornek.metot()`**).
    3. **Bir örnek üzerinde çalışır** (static değildir).
    
- Aşağıdaki örnekte associated function ve instance method verilmiştir.

```rust
struct Counter {
    value: i32,
}

// Counter için impl bloku
impl Counter {
    // Yeni bir Counter oluşturur (associated function)
    fn new() -> Self {
        Counter { value: 0 }
    }
    
    // Mevcut değeri döndürür (instance method)
    fn current(&self) -> i32 {
        self.value
    }
    
    // Sayacı artırır (mutable instance method)
    fn increment(&mut self) {
        self.value += 1;
    }
}

fn main() {
    let mut counter = Counter::new();  // 1. Yeni bir sayaç oluştur
    println!("Başlangıç: {}", counter.current()); // 0
    
    counter.increment();   // 2. Sayaç değerini artır
    println!("Artırıldı: {}", counter.current()); // 1
}
```

- Örnekte, associated function yapıyı başlatmak için fabrika metodu görevi görür. **`value = 0`** olan yeni bir sayaç döndürür.
- **`current`**, bir **instance method**'dur. Sayaç değerini döndürür (**`self.value`**).
- **`increment`**, bir **değiştirilebilir instance method**'dur. Sayaç değerini **`1`** artırır (**`self.value += 1`**).

---

<< [Day 6](https://github.com/zeyneptass/30-Days-Of-Rust/blob/main/Rust_Tutorial_Day_6/RustDay6.md) | [Day 8](https://github.com/zeyneptass/30-Days-Of-Rust/blob/main/Rust_Tutorial_Day_8/RustDay8.md) >>