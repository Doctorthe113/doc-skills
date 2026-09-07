# Code comments

1. **Function context.** Add one concise comment directly above every function, method, or assigned function whose name and signature do not already explain it. State what the function does, why it exists, and what it returns in simple words. Skip the comment when the name and signature answer those questions; `getUser(id: string): Promise<User>` needs nothing, and a comment that restates the name is noise.

```ts
// Add an item to the user's cart; addToCart enforces ownership and stock.
async function addItemToCart(userId: string, itemId: string): Promise<Cart> {
  return addToCart(userId, itemId);
}
```

2. **Large function bodies.** Use short comments to separate large chunks of related work. Group validation, database queries, mutations, and the response together. Name the purpose of each group.

```ts
// Add an item to the user's cart after validation and lookup.
async function addItemToCart(req: Request, res: Response): Promise<Response> {
  // Load: verify the user, then read the cart and the requested item.
  await verifyUser(req);
  const cart = await getCart(req.user.id);
  const item = await getItem(req.body.itemId);

  // Mutate: add the item and return the updated cart.
  await addToCart(cart, item);
  return res.status(200).json(cart);
}
```

3. **Comment size and quality.** Keep comments small, concise, and informative. A good comment gives context. A weak comment repeats the function name, next line, or obvious syntax; uses a vague label such as `// Handle edge case`; records temporary history; or describes behavior the code does not enforce.

4. **File sections.** Use one single-line comment above each major section of a file, immediately before the section it labels. Use plain labels such as `// Types`, `// Constants`, or `// Request handlers`.

```ts
// Global constants
const GLOBAL_VARIABLE = 2;

// Request handlers
function handleRequest(request: Request): Response {
  return createResponse(request);
}
```

5. **Separator style.** Do not use `// ---`, repeated dashes, boxed banners, or multi-line separator blocks. Keep function context comments, chunk comments, and file section comments to one line and at most 80 characters. Move detailed rationale, long explanations, and implementation notes into surrounding documentation or code structure.

6. **Markup.** In JSX, TSX, HTML, and other markup, use exactly one comment form: a short `{/* heading */}` separator between major layout regions, immediately before the section it labels. Keep logic, rationale, accessibility notes, implementation details, and temporary debugging notes in surrounding code or component documentation.

Done when every eligible function has a context comment, every large function has clear chunk boundaries, every major file section has a single-line label, no large or `// ---` separator comments exist, and markup uses only the allowed short separator form.
