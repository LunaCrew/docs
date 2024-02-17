# Tipos

Estes tipos são usados para impor uma determinada estrutura aos dados em toda a base de código, tornando o código mais previsível e menos sujeito a erros.

### Exemplos de tipos

```typescript
/**
 * Representa um serviço de usuário.
 * Pode ser um objeto, null ou indefinido.
 */
export type UserService = object | null | undefined

/**
 * Representa o resultado da criação de um documento.
 * @template T – O tipo do documento.
 */
export type CreateDocument = InsertOneResult<Document>
```
