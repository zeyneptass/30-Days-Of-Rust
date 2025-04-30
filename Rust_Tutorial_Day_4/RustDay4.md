# Rust Gün 4 : 

- Önceki dersimizde döngüler ve koşul ifadelerine değindik.
- Bugün Rust’ta fonksiyonlar ve kapsam (scope) yönetimine değineceğim.

# Fonksiyonlar :

- Rust'ta fonksiyonlar, programın temel yapı taşlarıdır.
- Kod tekrarını önlemek, programı modüler hale getirmek ve belirli görevleri yerine getirmek için kullanılan kod bloklarıdır.
- Fonksiyonlar, bir işlemi belirli girişler alarak gerçekleştirir ve sonuç döndürebilir.
- Rust'ta fonksiyonlar `fn` anahtar kelimesi ile tanımlanır
- Her Rust programı en az bir fonksiyon içerir: `main` fonksiyonu. Bu, programın giriş noktasıdır. Şimdiye kadar verdiğim örneklerde kodları hep main fonksiyonu içinde çalıştırdım.

***💡 Not :*** *Rust kodu, fonksiyon ve değişken adları için geleneksel stil olarak snake case'i kullanır; burada tüm harfler küçük harftir ve alt çizgiler kelimeleri ayırır.*

## Main Fonksiyonu :

- Rust programlarında **`main` fonksiyonu**, programın başlangıç noktasıdır.
- Rust programları `main` fonksiyonu olmadan çalıştırılamaz.
- Derleyici, programı çalıştırdığında **ilk olarak `main` fonksiyonunu çağırır**.
- **Rust’ta `main` fonksiyonu her zaman `fn main()` şeklinde tanımlanmalıdır** ve genellikle `()` (parametresiz) olur.
- Kıvrımlı parantezler derleyiciye fonksiyon gövdesinin nerede başladığını ve bittiğini söyler.

```rust
fn main() {
   println!("Merhaba, Rust!");
}
```

*💡 Not:  Bu kod çalıştırıldığında, `"Merhaba, Rust!"` ekrana yazdırılır. **`println!` bir makrodur ve ekrana çıktı vermek için kullanılır.***

## Fonksiyonlara Parametre Gönderme :

- Rust dilinde parametreli fonksiyonlar, belirli bir işlevi gerçekleştirmek için dışarıdan veri almak amacıyla kullanılır.
- Parametreler, fonksiyonun davranışını özelleştirmek veya farklı girdilere göre farklı sonuçlar üretmek için kullanılır. Bu, kodun yeniden kullanılabilirliğini ve modülerliğini artırır.
- Parametreler fonksiyonun imzasının parçası olan özel değişkenlerdir.
- Rust'ta parametreli fonksiyonlar, `fn` anahtar kelimesi ile tanımlanır. Parametreler, fonksiyon adından sonra parantez içinde belirtilir.
- Her parametrenin bir adı ve türü vardır.
- Fonksiyon imzalarında, her parametrenin türünü bildirmelisiniz çünkü derleyici, parametrelerin türünü otomatik olarak tahmin etmez.
- Fonksiyon tanımlamalarında tür açıklamalarının gerekli olması, derleyicinin ne tür bir değer kastettiğinizi anlamak için bu türleri kodun başka bir yerinde kullanmanıza neredeyse hiç ihtiyaç bırakmaz.
- Parametre türleri, Rust'ın güçlü tip sistemi sayesinde derleme zamanında kontrol edilir.

Parametre Alan Bir Fonksiyon Örneği :

```rust
fn greet(name: &str) { 
    println!("Merhaba, {}!", name); 
}
```

   *Yukarıdaki fonksiyon &str türünde bir  name değişkeni alır.*

İki Parametre Alan Bir Fonksiyon Örneği :

```rust
fn add(x: i32, y: i32) {
    println!("Toplam: {}", x + y);
}
```

   *Yukarıdaki fonksiyon x ve y adında türü i32 olan iki değişken alır. Bu değişkenlerinin toplamını ekrana yazdırır.*

Fonksiyon Çağırma : 

Rust dilinde fonksiyon çağırma için; fonksiyon adını ve gerekli parametreleri belirterek çağrı yapabilirsiniz.

```rust
fn main() {
   // Fonksiyon çağırma örnekleri:
   add(5, 3);       // Doğrudan değerlerle çağırma
   add(10, -2);     // Farklı değerlerle çağırma

   let a = 7;
   let b = 4;
   add(a, b);       // Değişkenlerle çağırma
	
   add(3 + 2, 4 * 2);  // İfadelerle çağırma

   // Toplama işlemi yapan fonksiyon
   fn add(x: i32, y: i32) {
       println!("Toplam: {}", x + y);
   }
}
```

Fonksiyon çağırırken dikkat edilmesi gereken iki husus vardır.

1. **Parametre Türleri Eşleşmeli**:
    - Fonksiyon **`i32`** bekliyorsa, **`String`** veya **`f64`** gönderemezsiniz.
    
    ```rust
    add(5.0, 3); // HATA! 5.0 f64 türünde, fonksiyon i32 bekliyor.
    ```
    
2. **Parametre Sayısı Eşleşmeli**:
    
    ```rust
    add(5); // HATA! İki parametre bekleniyor.
    ```
    

**Dönüş Değeri Olan Fonksiyon Çağırma :** 

- Eğer fonksiyon bir değer döndürüyorsa (örneğin toplam sonucunu döndürsün), çağırma şu şekilde yapılır:

```rust
fn main() {
    let result = add_with_return(5, 3);
    println!("Dönen değer: {}", result); // Dönen değer: 8
}

fn add_with_return(x: i32, y: i32) -> i32 {
    x + y  // Son ifade otomatik olarak dönüş değeri kabul edilir
}
```

*💡 Not: Rust fonksiyonlarınızı nerede tanımladığınızla ilgilenmez, sadece çağıran tarafından görülebilen bir kapsamda bir yerde tanımlanmaları önemlidir. add_with_return fonksiyonu main fonksiyonundan yukarıda da tanımlanabilirdi.*

### **Parametre Türleri**

Rust'ta parametreler herhangi bir türde olabilir:

- Temel türler: `i32`, `f64`, `bool`, vb.
- Bileşik türler: `String`, array, struct'lar, vb.
- Referanslar: `&T`, `&mut T`,`&str`

**Struct Parametresi Alan Fonksiyon Örneği:**

- Rust’ta bir `struct` tanımlayıp bunu bir fonksiyona parametre olarak verebiliriz.
- `struct`**kullanıcı tanımlı bir veri türüdür.** (user-defined type) Yani birden fazla veriyi bir arada tutmak için kullanılan **kendi özel veri türlerini** tanımlamayı sağlar.
    
    ```rust
    struct Kisi {
        isim: String,
        yas: u32,
    }
    
    fn kisi_bilgisi_yazdir(kisi: &Kisi) {
        println!("İsim: {}, Yaş: {}", kisi.isim, kisi.yas);
    }
    
    fn main() {
        let ali = Kisi {
            isim: String::from("Ali"),
            yas: 30,
        };
    
        kisi_bilgisi_yazdir(&ali);
    }
    
    ```
    

### Fonksiyon Dönüş Değerleri

- Rust’ta dönüş değerleri `->` ile belirtilir. Dönüş yapılacak ifadeye `return` yazmak isteğe bağlıdır. return yazarsak ifadenin sonuna `;` de eklemeliyiz.

```rust
fn carp(a: i32, b: i32) -> i32 {
    a * b // return olmadan da döner (son satır; noktalı virgül yok!)
}
```

- Eğer `;` kullanırsan dönüş olmaz: Çünkü noktalı virgül (`;`) eklenirse, extension’ı statment’’a çevirmiş oluruz rust gün 2’de İfade (Expression) ve Deyim (Statement) Arasındaki Fark bölümünde buna değindik. Ek olarak fonksiyon çağrıları ve bloklar da ifadedir: Yani yukarıdaki **a*b** bir extension’dur ve değer döndürür.

```rust
fn gecersiz_donus(a: i32, b: i32) -> i32 {
   a * b; // yanlış! çünkü ; ile ifade sonuç üretmez
}
```

- Return kullanılarak;

```rust
fn gecerli_donus(a: i32, b: i32) -> i32 {
   return a * b;  // Return kullanıldığı için sonuç üretir
}
```

---

**Makro :** 

- Rust'ta **makro ifadesi**, önceden tanımlanmış bir işlem grubunu (kod parçasını) daha kısa ve esnek bir şekilde yazmamızı sağlayan yapılardır.
- Fonksiyonlardan farklı olarak **makrolar kod üretir**, yani derleme zamanında çalışarak kodunuzu genişletir.
- Makrolar türden bağımsız, esnek kullanım sağlar.
- Makrolar `!` işareti ile çağrılırlar:
    
    ```rust
    println!("Merhaba, dünya!");
    ```
    
- Yukarıdaki `println!` bir **makrodur**, bir fonksiyon değildir. Bunun dışında birçok yerleşik makro vardır. Daha sonra detaylı değineceğiz.

# Değişken Kapsamı (Scope) ve Gövde Kuralları

Rust'ta **değişken kapsamı (scope)** ve **gövde (block) kuralları**, dilin güvenli ve bellek yönetimini sağlayan temel özelliklerindendir.

### **1. Değişken Kapsamı (Scope)**

Bir değişkenin geçerli olduğu ve kullanılabildiği bölgeye **kapsam (scope)** denir. Rust'ta değişkenler, tanımlandıkları blok (**`{}`** içi) boyunca yaşar ve blok sonunda otomatik olarak **drop** edilir (bellekten temizlenir).

```rust
fn main() {
   let x = 5; // x burada tanımlandı
   {
	   let y = 10; // y sadece bu blok içinde geçerli
	   println!("x = {}, y = {}", x, y);
   }
   // println!("y = {}", y); // HATA! y bu kapsamın dışında
}
```

Bu örnekte; `x` değişkeni `main` fonksiyonunun tamamında geçerlidir.`y` değişkeni sadece iç blokta geçerlidir. Blok bittiğinde **hayattan çıkar** (drop edilir).

### **2. Gövde (Block) Kuralları**

- Bloklar (**`{}`**), ifadeleri gruplamak ve yeni bir kapsam oluşturmak için kullanılır.
- Bir blok içinde tanımlanan değişkenler, blok dışında **erişilemez**.
- Fonksiyonlar, `if`, `match`, `loop` gibi kontrol yapıları gövde kullanır.
- Bloklar bir **ifade (expression)** olarak kullanılabilir ve son değeri döndürebilir.
- Bir blok içerisindeki son ifade (noktalı virgül olmadan) o bloğun dönüş değeridir.

Örnekler:

```rust
fn main() {
   let sonuc = {
      let a = 3;
      a + 2 // Bu satırın sonunda ; yok => bu değeri döndürür
   };
   println!("Sonuç: {}", sonuc); // çıktı: Sonuç: 5
}
```

- *Yukarıdaki örnekte; iç blok `a + 2` ifadesini döndürür ve bu `sonuc` değişkenine atanır.*



---
<< [Day 3](https://github.com/zeyneptass/30-Days-Of-Rust/blob/main/Rust_Tutorial_Day_3/RustDay3.md) | [Day 5](https://github.com/zeyneptass/30-Days-Of-Rust/blob/main/Rust_Tutorial_Day_5/RustDay5.md) >>