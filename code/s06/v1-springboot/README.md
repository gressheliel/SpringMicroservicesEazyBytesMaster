## Contenido de la Sección 06 V1
- Java 21 y Spring Boot 4.0.0
- Externalizar configuración en Spring Boot
- @Value, Environment, @ConfigurationProperties
- Profiles en Spring Boot


### Enfoques para Externalizar configuracion en Spring Boot
- Para generar las imágenes de docker en los 3 MS, se sigue el enfoque de Jib-Google

1. Using @Value annotation
```
# Property in application.yml
build:
  version: "3.0" 
 
// In AccountsController.java
    private final IAccountsService iAccountsService;

    // Se usa para evitar el conflicto con el @Value de buildVersion
    public AccountsController(IAccountsService iAccountsService) {
        this.iAccountsService = iAccountsService;
    }

    @Value("${build.version}")
    private String buildVersion;
```
2. Using Environment (Variables de entorno)
```
    @Autowired
    private Environment environment;

    public void getProperty(){
        String propertValue = environment.getProperty("JAVA_HOME");
    }
```

3. Using @ConfigurationProperties
```
// DTO
@ConfigurationProperties(prefix = "accounts")
public record AccountsContactInfoDto(String message, Map<String, String> contactDetails, List<String> onCallSupport) {
}

// Controller
    @Autowired
    private AccountsContactInfoDto accountsContactInfoDto;
    
    
    @GetMapping("/contact-info")
    public ResponseEntity<AccountsContactInfoDto> getContactInfo() {
        return ResponseEntity
                .status(HttpStatus.OK)
                .body(accountsContactInfoDto);
    }

//Configuration en application.yml
accounts:
  message: "Welcome to EazyBank accounts related local APIs "
  contactDetails:
    name: "John Doe - Developer"
    email: "john@eazybank.com"
  onCallSupport:
    - (555) 555-1234
    - (555) 523-1345

// Main Spring Boot Application
@EnableConfigurationProperties(value = {AccountsContactInfoDto.class})    
```

### Profiles in Spring Boot
1. Crear archivos de propiedades:
  - application_prod.yml
  - application_qa.yml

2. Analizar que propiedades cambian de un ambiente a otro, y modificarlas.

3. Activar las propiedades en cada profile application_qa.yml, application_prod.yml
```

```

4. Listar en la configuración default, cuales son las probables configuraciones
   y definir la actual.
```
spring:
  config:
    import:
      - "application_qa.yml"
      - "application_prod.yml"
  profiles:
    active:
      - "qa"

# Old Version
spring:
  config:
    activate:
      on-profile: "prod"
# New Version      
spring:
  profiles:
    active:
      - "prod"
```

### Profiles en Spring Boot, Desde la línea de commandos
- ...\accounts> mvn clean package
- ...\accounts\target> java -jar accounts-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod --build.version="1.1" 

### Profiles en Spring Boot, Desde la IDE
- Click Izquierdo en AccountsApplication
- More Run/Debug -> Modify Run Configurations -> Program Arguments
  --spring.profiles.active=prod --build.version="1.1"

- Otra Forma (Eliminar los argumentos previos: Program Arguments)

- Click Izquierdo en AccountsApplication
- More Run/Debug -> Modify Run Configurations -> VM Options
  -Dspring.profiles.active=prod -Dbuild.version="1.2"
- Si la opción no está disponible(Modify Options -> Add VM Options)

- Otra Forma (Eliminar los argumentos previos: VM Options)
- More Run/Debug -> Modify Run Configurations -> Environment Variables
  SPRING_PROFILES_ACTIVE=prod;BUILD_VERSION=1.3
- Si la opción no está disponible(Modify Options -> Environment variables)


  








