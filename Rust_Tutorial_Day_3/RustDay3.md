
# Rust Gün 3 :


- Önceki dersimizde temel syntax ve değişkenlere değindik. Mutable ve immutable kavramlarıyla birlikte shadowing yöntemlerinden de bahsettk.
- Bugün ise Rust’taki koşul ifadelerine ve döngülere değineceğim.

---

# Kontrol Akışı: Koşullar ve Döngüler

Rust'ta if ifadeleri ve döngüler gibi kontrol akışı araçları, bir koşulun doğru olup olmadığına bağlı olarak kod çalıştırmanıza ve bir koşul karşılanana kadar kodu tekrar tekrar çalıştırmanıza olanak tanıyarak, program yürütme üzerinde temel bir kontrol sağlar.

## If - else :

- Kodu koşullara bağlı olarak dallandırmamızı sağlar.
- Rust'ta `if` ifadeleri C, C++ ve Python'dakine benzer. Ancak, Rust'ta `if` bir ifade olduğu için sonuç döndürebilir.
- `if` ifadesi bir koşulun doğru (`true`) olup olmadığını kontrol eder ve eğer doğruysa ilgili kod bloğunu çalıştırır. `else` ifadesi ise koşul yanlış (`false`) olduğunda çalışacak kodu belirtir.
- `else` ifadesi isteğe bağlı olarak eklenebilir. Bu, koşul yanlış (`false`) olduğunda çalıştırılacak alternatif bir kod bloğu sağlar. Eğer `else` ifadesi kullanılmazsa ve koşul yanlışsa, program `if` bloğunu atlar ve bir sonraki koda geçer.
- Tüm `if` ifadeleri, `if` anahtar kelimesiyle başlar ve ardından bir koşul gelir.

*Örnek:*

```rust
fn main() {
    let number = 7;
    if number < 5 { 
        println!("Koşul doğru, number 5'ten küçük."); // Bu satır çalışmaz.
    } else {
        println!("Koşul yanlış, number 5'ten büyük."); // Bu satır çalışır.
    }
}

```

- *Yukarıdaki örnekte; koşul `number` değişkeninin değerinin 5'ten küçük olup olmadığını kontrol eder. Koşul doğru (`true`) ise, koşuldan hemen sonra gelen küme parantezleri (`{}`) içindeki kod bloğu çalıştırılır. Koşullara bağlı olan bu kod bloklarına  `arms` da denir. Bu kodu çalıştırdığında, `number` değişkeni 7 olduğu için çıktı şu olacaktır: “Koşul yanlış, number 5'ten büyük.”*

- Rust'ta `if` koşulu mutlaka bir `bool` türünde olmalıdır. Eğer koşul `bool` değilse, Rust hata verecektir.

```rust
fn main() {
    let number = 3;

    if number {
        println!("number üçtü"); // hata: expected `bool`, found integer
    }
}
```

- *Yukarıdaki kod derlenmeyecektir çünkü `if` koşulu bir tamsayı (`integer`) değeri olan `3`'ü içeriyor. Rust, otomatik olarak tamsayıyı `bool` türüne dönüştürmez. Bu nedenle, koşulun açık bir şekilde `bool` olması gerekir.*

### **Birden Fazla Koşul ile if - else :**

- Birden fazla koşulu kontrol etmek için `else if` ifadesi kullanılabilir. Örneğin:
    
    ```rust
    fn main() {
        let number = 6;
    
        if number % 4 == 0 {
            println!("number 4'e bölünebilir.");
        } else if number % 3 == 0 {
            println!("number 3'e bölünebilir."); // Bu satır çalıştırılacak.
        } else if number % 2 == 0 {
            println!("number 2'ye bölünebilir.");
        } else {
            println!("number 4, 3 veya 2'ye bölünemez.");
        }
    }
    ```
    
    - *Yukarıdaki fonksiyon; 6 sayısı 3’ bölünebildiğinden “*number 3'e bölünebilir.” çıktısını verir. Rust, `if` ifadelerini sırayla kontrol eder ve ilk doğru (`true`) koşulun kod bloğunu çalıştırır. Diğer koşulları kontrol etmez. Bu nedenle, 6 sayısı 2'ye de bölünebilir olmasına rağmen, sadece ilk doğru koşulun çıktısı görüntülenir.
    
    ### **Bir let ifadesinde if kullanımı :**
    
    - `if` bir ifade olduğu için, `let` ifadesinin sağ tarafında kullanılarak sonucu bir değişkene atanabilir. Örneğin;
        
        ```rust
        fn main() 
            let condition = true; 
            let number = if condition { 5 } else { 6 };
        
            println!("number değişkeninin değeri: {number}"); // number değişkeninin değeri: 5
        }
        // Yukarıdaki kodda, if ifadesi bir ifade olduğu için, if ifadesinin sonucunu bir değişkene atayabiliriz.
        ```
        
        - *Bu kod çalıştırıldığında, `condition` değişkeni `true` olduğu için `number` değişkeni 5 değerini alacak ve çıktı şu olacaktır: “number değişkeninin değeri: 5"*
- `if` ve `else` kollarındaki değerler aynı türde olmalıdır. Farklı türlerde değerler kullanılırsa, Rust hata verecektir. Örneğin:
    
    ```rust
    fn main() {
        let condition = true;
    
        let number = if condition { 5 } else { "six" }; // error: expected integer, found &str
    
        println!("number değişkeninin değeri: {number}");
    }
    ```
    
    - *Bu kod derlenmeyecektir çünkü `if` kolu bir tamsayı (`integer`) döndürürken, `else` kolu bir string (`&str`) döndürüyor. Rust, değişkenlerin türlerinin derleme zamanında kesin olarak bilinmesini gerektirir.*
    - Rust'ın derleme zamanında number değişkeninin türünü kesin olarak bilmesi gerekir. Bu sayede; runtime’da karmaşık tür kontrolleri yapmak yerine, derleyici tür güvenliğini garanti edebilir.

---

## **Döngülerle Tekrarlama :**

- Rust'ta bir kod bloğunu birden fazla kez çalıştırmak için üç tür döngü bulunur: `loop`, `while` ve `for`. Bu döngüler, belirli bir işlemi tekrarlamak veya bir koleksiyon üzerinde gezinmek için kullanılır.

### **1. Loop Döngüsü :**

- `loop` döngüsü, içindeki kodu sonsuza kadar tekrarlar veya programcı açıkça durdurana kadar çalışır.

```rust
fn main() {
    loop { 
        println!("Sonsuz döngü!");
    }
}
```

***💡 Not :**  Bu program çalıştırıldığında, terminalde sürekli olarak "Sonsuz döngü!" yazdırılır. Programı durdurmak için `Ctrl+C` kullanmanız gerekir.*

---

### **break ve continue :**

- Rust'ta döngülerin akışını kontrol etmek için kullanılırlar.
    
    `break`: Döngüyü tamamen sonlandırır ve programın döngüden sonraki koda devam etmesini sağlar. Genellikle belirli bir koşul sağlandığında döngüyü durdurmak için kullanılır.
    
    ```rust
    fn main() {
        let mut count = 0; 
        loop {
            count += 1; // count = count + 1
            println!("Count: {}", count);
    
            // Eğer count 5'e ulaşırsa döngüyü durdur
            if count == 5 {
                break; // Döngüyü durdur
            }
        }
        println!("Döngü sonlandı!"); 
    }
    ```
    
    - *Yukarıdaki fonksiyon; 1'den 5'e kadar sayıları ekrana yazdırır  count 5'e ulaştığında break ifadesi ile döngüyü durdurur.*
        
        Çıktı : 
        
        ```
        Count: 1
        Count: 2
        Count: 3
        Count: 4
        Count: 5
        Döngü sonlandı!
        ```
        
    
    ***💡 Not :***  *Rust'ta loop döngüleri **sonsuz döngüler** olduğu için, döngüyü bir noktada durdurmak için mutlaka `break` komutunu kullanmanız gerekir. loop döngüsü, programcı tarafından açıkça durdurulmadığı sürece sonsuza kadar çalışır. Bu nedenle, döngüyü kontrol etmek ve belirli bir koşul sağlandığında sonlandırmak için break ifadesi kullanılır.*
    
    **Break yerine return kullanılabilir mi?**
    
    - Evet, `break` yerine `return` kullanarak da döngüyü sonlandırabilirsiniz. Ancak, `return` ifadesi sadece döngüyü değil, bulunduğu fonksiyonu da sonlandırır.
        
        ```rust
        fn main() {
            let mut count = 0;
        
            loop {
                count += 1;
                println!("Count: {}", count);
        
                if count == 5 {
                    return; // Fonksiyondan çık
                }
            }
            // Bu kod asla çalışmaz
            println!("Döngü sonlandı!"); // unreachable code error
        }
        ```
        
        *Yukarıdaki kodu çalıştırdığınızda, döngü 5 kez çalışacak ve ardından program sona erecektir. **println!("Döngü sonlandı!");** fonksiyonu asla çalışmayacaktır.*
        
        Çıktı : 
        
        ```
        Count: 1
        Count: 2
        Count: 3
        Count: 4
        Count: 5
        ```
        
        ***💡 Not :**  Rust'ta `return` ifadesi, bir fonksiyondan değer döndürmek için kullanılabilir. Bu, özellikle fonksiyonun belirli bir noktada sonlanması ve bir değer döndürmesi gerektiğinde kullanışlıdır.*
        
- `continue`: Döngünün mevcut turunu atlar ve bir sonraki turuna geçer.  Yani, `continue` ifadesi çalıştığında, döngünün geri kalan kodu atlanır ve döngü bir sonraki iterasyona devam eder.
    
    ```rust
    fn main() {
        let mut number = 1;
        loop {
            // Eğer number 10'dan büyükse döngüyü sonlandır
            if number > 10 {
                break; // Döngüyü sonlandır
            }
            // Eğer number çift ise bu turu atlar
            if number % 2 == 0 {
                number += 1; // number'ı artırmayı unutma!
                continue; // Bu turu atla
            }
            println!("Tek sayı: {}", number);
            number += 1; // number'ı bir sonraki sayıya geçir
        }
        println!("Döngü tamamlandı!"); // Döngü tamamlandığında bu satır çalışır
    }
    ```
    
    - *Yukarıdaki fonksiyon; 1'den 10'a kadar olan tek sayıları ekrana yazdırır. `Continue`ifadesi ile çift sayıları atlar `break`ifadesi ile de fonksiyonu 10’a kadar çalıştırır.*
        
        Çıktı : 
        
        ```
        Tek sayı: 1
        Tek sayı: 3
        Tek sayı: 5
        Tek sayı: 7
        Tek sayı: 9
        Döngü tamamlandı!
        ```
        
    
    ---
    
    ### Birden Fazla Döngü Arasındaki Belirsizliği Gidermek İçin Döngü Etiketleri
    
    - Eğer iç içe döngüleriniz varsa, `break` ve `continue` ifadeleri varsayılan olarak **en içteki döngüyü** etkiler.
    - Ancak, bir döngüye **etiket** ekleyerek, `break` veya `continue` ifadelerinin bu etiketli döngüyü hedef almasını sağlayabilirsiniz.
    - Döngü etiketleri, tek tırnak (`'`) ile başlar.
        
        İşte iki iç içe döngü içeren bir örnek:
        
        ```rust
        fn main() {
            let mut count = 0;
            'counting_up: loop { // Dış döngü
                println!("count = {count}"); // count = 0, 1, 2
                let mut remaining = 10;
        
                loop { // İç döngü
                    println!("remaining = {remaining}");
                    if remaining == 9 {
                        break; // İç döngüyü sonlandır
                    }
                    if count == 2 {
                        break 'counting_up; // Dış döngüyü sonlandır
                    }
                    remaining -= 1;
                }
                count += 1; // count = 0, 1, 2
            }
            println!("End count = {count}"); // count = 2
        }
        ```
        
        - *Yukarıdaki fonksiyonun amacı, iç döngüdeki `remaining` değişkenini 9'a eşitlerken, dış döngüdeki `count` değişkenini 2'ye eşitlediğinde dış döngüyü sonlandırmaktır. Bu durumda, `count` değişkeni 2 olacak ve dış döngü sonlandırılacaktır. `End count = 2` çıktısı alınacaktır. Yani, iç döngüdeki `remaining` değişkeni 9'a eşit olurken, dış döngüdeki `count` değişkeni 2'ye eşit olduğunda dış döngüyü sonlandırmak için etiketli döngü kullanılmıştır.*
        
        **Kodun Açıklaması:**
        
        1. **Dış Döngü (`'counting_up`):**
            - `'counting_up` etiketiyle işaretlenmiş bir döngü başlatılır.
            - Bu döngü, `count` değişkenini 0'dan başlatır ve her turda `count` değerini bir artırır.
        2. **İç Döngü:**
            - İç döngü, `remaining` değişkenini 10'dan başlatır ve her turda `remaining` değerini bir azaltır.
            - Eğer `remaining` 9'a eşit olursa, `break` ifadesi çalışır ve **iç döngü** sonlanır.
            - Eğer `count` 2'ye eşit olursa, `break 'counting_up;` ifadesi çalışır ve **dış döngü** sonlanır.
        3. **Döngülerin Sonlanması:**
            - İç döngü, `remaining` 9 olduğunda sonlanır.
            - Dış döngü, `count` 2 olduğunda sonlanır.
        4. **Programın Çıktısı:**
            - Program çalıştırıldığında aşağıdaki çıktıyı verir:
        
        Çıktı : 
        
        ```
        count = 0
        remaining = 10
        remaining = 9
        count = 1
        remaining = 10
        remaining = 9
        count = 2
        remaining = 10
        End count = 2
        ```
        
        Yukarıdaki örnekte dış döngüye etiket (`'counting_up`) eklemeseydik ve `break 'counting_up;` yerine sadece `break` kullansaydık, programın davranışı tamamen değişirdi. İşte bu durumda nasıl bir çıktı alacağımızı adım adım inceleyelim:
        
        ```rust
        fn main() {
            let mut count = 0;
            loop { // Dış döngüye etiket eklenmedi
                println!("count = {count}");
                let mut remaining = 10;
                loop {
                    println!("remaining = {remaining}");
                    if remaining == 9 {
                        break; // İç döngüyü sonlandır
                    }
                    if count == 2 {
                        break; // Sadece iç döngüyü sonlandırır, dış döngüyü değil
                    }
                    remaining -= 1;
                }
                count += 1;
            }
            println!("End count = {count}"); // unreachable statement : yani bu kod çalıştırılmaz
        }
        ```
        
        - *Yukarıdaki kodda, iç döngüdeki break ifadesi sadece iç döngüyü sonlandırır. Dış döngüyü sonlandırmak için etiket eklemediğimizden dolayı, dış döngü sonsuz döngü olur ve program asla sonlanmaz. Bu durumda, println!("End count = {count}"); kodu asla çalıştırılmaz. Bu kodu çalıştırmak için iç döngüdeki break ifadesine etiket eklememiz gerekir.*
        
        Çıktı : 
        
        ```
        count = 0
        remaining = 10
        remaining = 9
        count = 1
        remaining = 10
        remaining = 9
        count = 2
        remaining = 10
        remaining = 9
        count = 3
        remaining = 10
        remaining = 9
        count = 4
        remaining = 10
        remaining = 9
        count = 5
        remaining = 10
        remaining = 9
        ...
        ```
        
    
    ### 2. While Döngüsü :
    
    Genellikle bir döngü içinde bir koşulu değerlendirme kullanır. 
    
    Koşul doğru (`true`) olduğu sürece döngü çalışır. Koşul yanlış (`false`) olduğunda, döngü sona erer.
    
    Örnek:
    
    ```rust
    fn main() {
        let mut sayı = 5;
    
        while sayı > 0 {
            println!("Sayı: {}", sayı);
            sayı -= 1; // Her turda sayıyı azalt
        }
    
        println!("Döngü bitti!"); // Döngü bittiğinde bu satır çalışır
    }
    ```
    
    ***💡 Not :**  Yukarıdaki fonksiyon; sayı değişkenini 5 olarak tanımlar ve sayı 0'dan büyük olduğu sürece döngüyü çalıştırır. Her turda sayıyı azaltır ve sayıyı ekrana yazdırır. Döngü bittiğinde "Döngü bitti!" yazısını ekrana yazdırır.*
    
    Çıktı :
    
    ```
    Sayı: 5
    Sayı: 4
    Sayı: 3
    Sayı: 2
    Sayı: 1
    Döngü bitti!
    ```
    
    **Bir Koleksiyonda Döngü Oluşturma :** 
    
    `while` döngüsünü, bir koleksiyonun (örneğin bir dizinin) elemanları üzerinde gezinmek için de kullanabilirsiniz. Aşağıdaki örnekte, bir dizinin her elemanı yazdırılıyor:
    
    ```rust
    fn main() {
        let a = [10, 20, 30, 40, 50];
        let mut index = 0;
    
        while index < 5 {
            println!("the value is: {}", a[index]); 
    
            index += 1;
        }
    }
    ```
    
    *Yukarıdaki fonksiyon; a dizisinin elemanlarını ekrana yazdırmak için bir döngü kullanmaktadır. index değişkeni, dizinin elemanlarına erişmek için kullanılmaktadır. index değişkeni, 0'dan başlayarak 5'e kadar arttırılmaktadır. Bu durumda döngü, dizinin tüm elemanlarını ekrana yazdıracaktır ve index 5 olduğunda döngü sona erecektir.*
    
    Çıktı : 
    
    ```
    the value is: 10
    the value is: 20
    the value is: 30
    the value is: 40
    the value is: 50
    ```
    
    **while Döngüsünün Dezavantajları :**
    
    1. **Hata Riski:**
        - Eğer dizinin boyutunu değiştirirseniz (örneğin 5 elemanlı bir diziyi 4 elemanlı yaparsanız) ve `while index < 5` koşulunu güncellemezseniz, program **panikleyebilir** (hata verir).
        - Bu tür hatalar, özellikle büyük projelerde tespit edilmesi zor olabilir.
    2. **Performans:**
        - `while` döngüsü, her turda dizinin sınırlarını kontrol etmek için ekstra bir koşul gerektirir. Bu, performansı olumsuz etkileyebilir.
        
        For döngü kullanarak koleksiyondaki her öğe iterasyon yapmak hata riskini azaltıp performansı arttırır. 
        
    

### 3. For Döngüsü :

- Rust'ta `for` döngüsü, belirli bir koleksiyon (dizi, vektör, aralık vb.) üzerinde dolaşmak için kullanılır.
- Diğer dillerdeki foreach döngüsüne benzer.
- Temel amacı, bir koleksiyonun her elemanını kolay ve güvenli bir şekilde işleyebilmektir bu yüzden genellikle `while` yerine tercih edilir çünkü daha güvenlidir ve dizinin sınırlarını aşma riski yoktur.

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a {
        println!("the value is: {element}");
    }
}
```

*Yukarıdaki fonskiyon; for döngüsü ile bir dizi üzerinde dolaşırken, dizi elemanlarını kopyalar ve bu elemanları ekrana yazdırır. Çıktı while döngüsündekiyle aynı olur ama while döngüsünden daha güvenlidir.*

**Kodun Açıklaması :** 

- `for element in a` ifadesi, dizinin her elemanını sırayla `element` değişkenine atar.
- Her eleman ekrana yazdırılır.
- Dizinin boyutunu değiştirseniz bile, `for` döngüsü otomatik olarak uyum sağlar.

**For Döngüsünün Avantajları :** 

1. **Güvenlik:**
    - `for` döngüsü, dizinin sınırlarını otomatik olarak kontrol eder. Bu, dizinin sonunu aşma (out-of-bounds) hatalarını önler.
2. **Özlülük:**
    - `for` döngüsü, `while` döngüsüne kıyasla daha az kod gerektirir ve daha okunabilirdir.
3. **Esneklik:**
    - Dizinin boyutunu değiştirseniz bile, `for` döngüsü otomatik olarak uyum sağlar.

**For Döngüsü ile Geri Sayım :**

`for` döngüsü, bir aralık (`Range`) üzerinde de kullanılabilir. Örneğin, geri sayım yapmak için aşağıdaki kodu kullanabilirsiniz:

```rust
fn main() {
    for number in (1..4).rev() { // 3, 2, 1 gerisayımı yapar 4 dahil değil
        println!("{number}!");
    }
    println!("KALKIŞ!"); 
}
```

**Kodun Açıklaması:**

- `(1..4)` aralığı, 1'den 3'e kadar olan sayıları üretir.
- `.rev()` metodu, bu aralığı tersine çevirir (yani 3, 2, 1 şeklinde).
- Her sayı ekrana yazdırılır ve sonunda "LIFTOFF!!!" mesajı yazdırılır.

Çıktı : 

```rust
3!
2!
1!
KALKIŞ!
```

---
<< [Day 1](https://github.com/zeyneptass/30-Days-Of-Rust/blob/main/Rust_Tutorial_Day_1/RustDay2.md) | [Day 3](https://github.com/zeyneptass/30-Days-Of-Rust/blob/main/Rust_Tutorial_Day_3/RustDay3.md) >>