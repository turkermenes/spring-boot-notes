# @ConfigurationProperties

`@ConfigurationProperties` dipnotu application.yml dosyasındaki özellikleri kolay bir şekilde uygulamamızın istenilen kısmında enjekte edilerek kullanılmasına imkan sağlamaktadır.

Bu şekilde bir application.yml dosyasına sahipsek:

application.yml

```yaml
app:
  name: Test
  title: Testing
  release: 2025
```

Bu işlemi hem class ile hem de record ile yapmamız mümkündür. Record ile yapmamız halinde daha az kod gerekeceği için onu tercih edeceğiz. application.yml dosyasında “app” prefixine sahip olan özellikleri enjekte edebilmek için AppProperties isminde bir record oluşturalım ve özellikleri parametre olarak girelim. Üzerine de `@ConfigurationProperties(prefix = “app”)` dipnotunu ekleyelim:

AppProperties.java

```java
// importlar

@ConfigurationProperties(prefix = "app")
public record AppProperties(String name, String title, String release) {}
```

record olarak tanımlamamızın sonucu olarak Spring Boot uygulamamızın main metodunu barındıran sınıfa `@EnableConfigurationProperties(AppProperties.class)` dipnotunu eklememiz gerekmektedir:

DemoApplication.java

```java
// importlar

@SpringBootApplication
@EnableConfigurationProperties(AppProperties.class)
public class DemoApplication {
		
		public static void main(String[] args) {
				SpringApplication.run(DemoApplication.class, args);
		}
}
```

Artık bu AppProperties kaydını istediğimiz yerde enjekte ederek özelliklere erişim sağlayabiliriz. Örnek olarak bir AppController isminde Controller sınıfı oluşturalım. Ardından AppProperties kaydını yapılandırıcı enjeksiyonu yapıp [http://localhost:8080/properties](http://localhost:8080/properties) adresi için GET metodu API’si yazalım:

AppController.java

```java
// importlar

@RestController
public class AppController {

		private final AppProperties appProperties;
		
		public AppController(AppProperties appProperties) {
				this.appProperties = appProperties;
		}
		
		@GetMapping("/properties")
		public String getAppProperties() {
				return appProperties.name() + ", " + appProperties.title() 
								+ ", " + appProperties.release();
		}

}
```

İşlem tamam! Artık Spring Boot uygulamamızı başlatıp test edebiliriz. Proje klasöründe terminale `./mvnw spring-boot:run` yazarak uygulamamızı başlatalım. Konsol çıktılarını kontrol edelim eğer herhangi bir yerde yanlış yapmamışsak hata çıktısı görmemeliyiz. Eğer hata çıktısı yoksa artık [http://localhost:8080/properties](http://localhost:8080/properties) adresine gidebiliriz. Beklendiği gibi “Test, Testing, 2025” şeklinde bir sayfa bizi karşılamalıdır. 

Tebrikler! Bu eğitimde application.yml dosyasında yer alan özellikleri kolay bir şekilde uygulamamızda kullanmayı öğrenmiş olduk.