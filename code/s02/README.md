## Contenido de la Sección 02 
- Java 21 y Spring Boot 4.0.0
- Creación de Servicios
  - accounts, cards, loans
- La estructura de Accounts es similar a la de Cards y Loans


### Servicio Accounts
- Utiliza H2 Database
- Constructor privado para las constantes.
- MapStruct & ModelMapper, no son oficialmente recomendadas por Spring
- Manejo de excepciones con GlobalHandlerException
  - @ControllerAdvice, @ExceptionHandler(Exception.class)...
  - GlobalExceptionHandler  extends ResponseEntityExceptionHandler
- Manejo de transacción cuando se borran los datos(Accounts), en el Repository
  ```
      @Transactional
      @Modifying
      void deleteByCustomerId(Long customerId);
  ```
- Dentro del ExceptionHandler, puede ir cualquier lógica
- Para testear las RuntimeException , se comenta : @AllArgsConstructor en el Controller y se envía alguna petición.
  - Se lanzara : 500 INTERNAL_SERVER_ERROR

- Manejo de Validaciones
  - Dependencia : spring-boot-starter-validation
  - DTO's : @NotEmpty, @Size, @Email, @Pattern(regexp="(^$|[0-9]{10})",message="")...
  - Controller nivel class : @Validated [Realiza validaciones en todas las API REST, definidas en el controller]
  - Controller param metodos: @Valid @RequestBody  DTO's  (Orden es importante) [Realiza validaciones en el cuerpo de la petición]
  - Controller un solo param: @RequestParam @Pattern(...) 
    - Valida cuando es solo un parámetro en : deleteAccountDetails y fetchAccountDetails
  - Mensajes : GlobalExceptionHandler  extends ResponseEntityExceptionHandler
    - Sobrescribir : protected ResponseEntity<Object> handleMethodArgumentNotValid{...}
    - Para personalizar los mensajes de validación de los DTO's

- Auditar datos
  - En la BD existen estas columnas : CREATED_AT, CREATED_BY, UPDATED_AT, UPDATED_BY
  - BaseEntity agregar :   @CreatedDate, @CreatedBy, @LastModifiedDate, @LastModifiedBy
  - Para devolver quien realiza la creación o update:
    ```
    @Component("auditAwareImpl")
    public class AuditAwareImpl implements AuditorAware<String> {...}
    ```
  - BaseEntity agregar : @EntityListeners(AuditingEntityListener.class)
  - Main class AccountsApplication agregar : @EnableJpaAuditing(auditorAwareRef = "auditAwareImpl")

- Documentación OpenAPI 3 (Swagger UI)
  - http://localhost:8080/swagger-ui/index.html
  - Dependencia : springdoc-openapi-starter-webmvc-ui
  - En AccountsApplication :  @OpenAPIDefinition(...)
  - En Controllers : @Tag(...) , @Operation(...), @ApiResponses(...)
  - En DTO's @Schema
  - En los @ApiResponse del controller se agrega la información del ErrorResponseDTO

- Si no se tiene el Main en el paquete raíz, se debe agregar :@ComponentScan en el Main
```
@SpringBootApplication
@ComponentScans({ @ComponentScan("com.eazybytes.accounts.controller") })
@EnableJpaRepositories("com.eazybytes.accounts.repository")
@EntityScan("com.eazybytes.accounts.model")
public class AccountsApplication {...}
```
 