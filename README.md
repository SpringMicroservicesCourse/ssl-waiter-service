# SSL 服務端服務 ⚡

[![Java](https://img.shields.io/badge/Java-21-orange.svg)](https://www.oracle.com/java/)
[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2.5-brightgreen.svg)](https://spring.io/projects/spring-boot)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 專案介紹

這是一個展示 SSL/TLS 安全通訊在 Spring Boot 微服務架構中應用的服務端專案。主要功能是模擬咖啡廳的服務端，提供咖啡商品管理和訂單處理的 RESTful API 服務，透過 SSL/TLS 協議確保客戶端與服務端之間的安全通訊。

### 🎯 專案特色

- **SSL/TLS 安全通訊** - 使用自簽憑證確保 HTTPS 安全連線
- **RESTful API 設計** - 標準化的 REST API 端點設計
- **JPA 資料持久化** - 使用 Hibernate 進行物件關聯對應
- **快取機制** - 整合 Spring Cache 提升效能
- **效能監控** - 內建效能攔截器和 Actuator 監控
- **多格式支援** - 支援 JSON、表單資料和檔案上傳

> 💡 **為什麼選擇此專案？**
> - 展示 SSL/TLS 在 Spring Boot 中的完整實作
> - 提供完整的微服務架構範例
> - 適合學習安全通訊和 API 設計
> - 程式碼結構清晰，便於理解和擴展

## 技術棧

### 核心框架
- **Spring Boot 3.2.5** - 現代化的 Java 應用程式框架
- **Java 21** - 最新的 Java 版本，支援現代化語法特性
- **Spring Data JPA** - 物件關聯對應框架
- **H2 Database** - 輕量級記憶體資料庫

### 開發工具與輔助
- **Lombok** - 減少樣板程式碼，提升開發效率
- **Joda Money 2.0.2** - 專業的貨幣處理庫
- **Apache Commons Lang3 3.17.0** - 實用的工具類庫
- **Jackson Hibernate6** - Hibernate 實體序列化支援

## 專案結構

```
ssl-waiter-service/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── tw/fengqing/spring/springbucks/waiter/
│   │   │       ├── WaiterServiceApplication.java     # 主應用程式類別
│   │   │       ├── controller/                      # 控制器層
│   │   │       │   ├── CoffeeController.java        # 咖啡商品控制器
│   │   │       │   ├── CoffeeOrderController.java   # 訂單控制器
│   │   │       │   ├── PerformanceInteceptor.java   # 效能攔截器
│   │   │       │   └── request/                     # 請求模型
│   │   │       ├── model/                           # 資料模型
│   │   │       │   ├── Coffee.java                  # 咖啡商品模型
│   │   │       │   ├── CoffeeOrder.java             # 訂單模型
│   │   │       │   ├── BaseEntity.java              # 基礎實體類別
│   │   │       │   ├── MoneyConverter.java          # 貨幣轉換器
│   │   │       │   └── OrderState.java              # 訂單狀態列舉
│   │   │       ├── service/                         # 服務層
│   │   │       ├── repository/                      # 資料存取層
│   │   │       └── support/                         # 支援類別
│   │   └── resources/
│   │       ├── application.properties               # 應用程式設定檔
│   │       ├── schema.sql                          # 資料庫結構定義
│   │       ├── data.sql                            # 初始資料
│   │       ├── coffee.txt                          # 咖啡資料檔案
│   │       └── springbucks.p12                     # SSL 憑證檔案
│   └── test/
├── pom.xml
└── README.md
```

## 快速開始

### 前置需求
- **Java 21** - 確保已安裝 JDK 21 或更新版本
- **Maven 3.6+** - 用於專案建置和依賴管理
- **SSL 憑證** - 確保 `springbucks.p12` 憑證檔案存在於 resources 目錄

### 安裝與執行

1. **克隆此倉庫：**
```bash
git clone https://github.com/SpringMicroservicesCourse/ssl-waiter-service.git
```

2. **進入專案目錄：**
```bash
cd ssl-waiter-service
```

3. **編譯專案：**
```bash
mvn compile
```

4. **執行應用程式：**
```bash
mvn spring-boot:run
```

## 進階說明

### 環境變數
```properties
# 伺服器設定
server.port=8443
server.ssl.key-store=classpath:springbucks.p12
server.ssl.key-store-type=PKCS12
server.ssl.key-store-password=spring

# JPA 設定
spring.jpa.hibernate.ddl-auto=none
spring.jpa.properties.hibernate.show_sql=true
spring.jpa.properties.hibernate.format_sql=true

# Actuator 監控設定
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=always
```

### 設定檔說明
```properties
# application.properties 主要設定
server.port=8443                                    # HTTPS 服務埠號
server.ssl.key-store=classpath:springbucks.p12     # SSL 憑證檔案路徑
server.ssl.key-store-password=spring               # 憑證密碼
spring.jpa.hibernate.ddl-auto=none                 # 不自動建立資料表
management.endpoints.web.exposure.include=*        # 開放所有監控端點
```

### 核心程式碼說明

#### SSL 伺服器配置
```java
@SpringBootApplication
@EnableJpaRepositories
@EnableCaching
public class WaiterServiceApplication implements WebMvcConfigurer {
    
    public static void main(String[] args) {
        // 啟動 Spring Boot 應用程式，啟用 SSL 支援
        SpringApplication.run(WaiterServiceApplication.class, args);
    }
    
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 註冊效能攔截器，監控咖啡和訂單相關的 API 呼叫
        registry.addInterceptor(new PerformanceInteceptor())
                .addPathPatterns("/coffee/**").addPathPatterns("/order/**");
    }
}
```

#### REST API 控制器
```java
@RestController
@RequestMapping("/coffee")
@Slf4j
public class CoffeeController {
    
    @PostMapping(path = "/", consumes = MediaType.APPLICATION_JSON_UTF8_VALUE)
    @ResponseStatus(HttpStatus.CREATED)
    public Coffee addJsonCoffeeWithoutBindingResult(@Valid @RequestBody NewCoffeeRequest newCoffee) {
        // 處理 JSON 格式的新咖啡商品新增請求
        return coffeeService.saveCoffee(newCoffee.getName(), newCoffee.getPrice());
    }
    
    @GetMapping(path = "/", params = "!name")
    public List<Coffee> getAll() {
        // 取得所有咖啡商品清單
        return coffeeService.getAllCoffee();
    }
    
    @GetMapping("/{id}")
    public Coffee getById(@PathVariable Long id) {
        // 根據 ID 取得特定咖啡商品
        Coffee coffee = coffeeService.getCoffee(id);
        log.info("Coffee {}:", coffee);
        return coffee;
    }
}
```

#### 資料模型設計
```java
@Entity
@Table(name = "T_COFFEE")
@Builder
@Data
@EqualsAndHashCode(callSuper = true)
@ToString(callSuper = true)
@NoArgsConstructor
@AllArgsConstructor
public class Coffee extends BaseEntity implements Serializable {
    private String name;                    // 咖啡名稱
    @Convert(converter = MoneyConverter.class)
    private Money price;                    // 價格（使用 Joda Money）
}
```

## API 端點說明

### 咖啡商品管理
- `GET /coffee/` - 取得所有咖啡商品
- `GET /coffee/{id}` - 根據 ID 取得咖啡商品
- `GET /coffee/?name={name}` - 根據名稱取得咖啡商品
- `POST /coffee/` - 新增咖啡商品（支援 JSON 和表單格式）
- `POST /coffee/` - 批次新增咖啡商品（支援檔案上傳）

### 訂單管理
- `GET /order/{id}` - 根據 ID 取得訂單
- `POST /order/` - 建立新訂單

### 監控端點
- `GET /actuator/health` - 健康檢查
- `GET /actuator/info` - 應用程式資訊
- `GET /actuator/metrics` - 效能指標

## 參考資源

- [Spring Boot 官方文件](https://spring.io/projects/spring-boot)
- [Spring Data JPA 文件](https://spring.io/projects/spring-data-jpa)
- [SSL/TLS 安全通訊指南](https://docs.spring.io/spring-boot/docs/current/reference/html/features.html#features.ssl)
- [Joda Money 文件](https://www.joda.org/joda-money/)

## 注意事項與最佳實踐

### ⚠️ 重要提醒

| 項目 | 說明 | 建議做法 |
|------|------|----------|
| 安全性 | SSL 憑證管理 | 使用環境變數管理憑證密碼 |
| 效能 | 資料庫連線池 | 適當配置連線池大小 |
| 監控 | API 效能監控 | 實作完整的監控機制 |

### 🔒 最佳實踐指南

- **憑證管理** - 將敏感資訊如憑證密碼存放在環境變數中
- **API 設計** - 遵循 RESTful 設計原則，提供一致的 API 介面
- **錯誤處理** - 實作完整的異常處理機制
- **日誌記錄** - 使用結構化日誌記錄關鍵操作
- **單元測試** - 為核心業務邏輯撰寫完整的測試案例

### 🔧 開發建議

- **程式碼註解** - 在重要的程式碼區塊添加清楚註解，方便團隊成員理解與維護
- **命名規範** - 使用有意義的變數和方法名稱
- **模組化設計** - 將不同功能分離到不同的類別中
- **配置外部化** - 將設定值放在配置檔案中，便於環境切換
- **效能優化** - 使用快取機制提升應用程式效能

## 授權說明

本專案採用 MIT 授權條款，詳見 LICENSE 檔案。

## 關於我們

我們主要專注在敏捷專案管理、物聯網（IoT）應用開發和領域驅動設計（DDD）。喜歡把先進技術和實務經驗結合，打造好用又靈活的軟體解決方案。

## 聯繫我們

- **FB 粉絲頁**：[風清雲談 | Facebook](https://www.facebook.com/profile.php?id=61576838896062)
- **LinkedIn**：[linkedin.com/in/chu-kuo-lung](https://www.linkedin.com/in/chu-kuo-lung)
- **YouTube 頻道**：[雲談風清 - YouTube](https://www.youtube.com/channel/UCXDqLTdCMiCJ1j8xGRfwEig)
- **風清雲談 部落格**：[風清雲談](https://blog.fengqing.tw/)
- **電子郵件**：[fengqing.tw@gmail.com](mailto:fengqing.tw@gmail.com)

---

**📅 最後更新：2025年8月7日**  
**👨‍💻 維護者：風清雲談團隊**
