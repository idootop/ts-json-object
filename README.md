# `ts-json-object`

JSON object validation for typescript

## 快速开始

```typescript
import { SafeJSON, StrictJSON } from "ts-json-object";

// StrictJSON ( Book 构造参数类型跟随 Book 中定义的字段)
class Book extends StrictJSON<Book> {
  // 必选属性
  @required
  name: string;

  // 可选属性
  @optional
  summary?: string;
}

// 这样是 ok 的
let book: Book = new Book({
  name: "醒来觉得甚是爱你",
  summary: "朱光潜写给宋清如的情书💌",
});
// 这样也 ok
let b2: Book = new Book({ name: "醒来觉得甚是爱你" });
// Summary 为 undefined
b2.summary === undefined;

// （类型）报错，name 为必选属性
let book: Book = new Book({ summary: "与世界重修旧好" });
// （类型）报错，name 必须为 string 类型
let book: Book = new Book({ name: 12345 });

// SafeJSON ( Author 构造参数全部可选 )
class Author extends SafeJSON<Author> {
  @required
  name: string;
}

class BookWithAuthor extends SafeJSON<BookWithAuthor> {
  // 必选字段
  @required
  name: string;

  // 非空字段，自动填充默认值
  @optional("暂无简介")
  summary!: string;

  // 支持子对象
  @optional(new Author({ name: "路遥" }))
  author!: Author;

  // 支持对象数组
  @array(Author)
  @optional([])
  authors!: Author[];
}

// 这样是 ok 的
let book2: BookWithAuthor = new BookWithAuthor({
  name: "醒来觉得甚是爱你",
  author: { name: "朱光潜" },
});
// 自动填充默认值
book2 ==
  {
    name: "醒来觉得甚是爱你",
    summary: "暂无简介",
    author: { name: "朱光潜" },
    authors: [],
  };
// 类型正确
book2.author instanceof Author == true;

// interface/object

interface BookInterface {
  name: string;
  summary?: string;
}

type BookWithSize = Book & {
  size: "small" | "big";
};

// Book 类型符合 BookInterface
let book3: BookInterface = new Book({
  name: "醒来觉得甚是爱你",
  summary: "朱光潜写给宋清如的情书💌",
});
// Book 实例可当作常规的 object 解构/建构
let book4: BookWithSize = { ...book3, size: "big" };
let { size, ...book5 } = book4;
```

## 其他

适用于 tsc + es6 （ 不支持 babel / es5 ）

```json
// tsconfig.json
{
  // ...
  "compilerOptions": {
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    // ...
  }
}
```

## 鸣谢

原作者：[Moshe Gottlieb](https://sharkfood.com)

更多细节，请参考原始文档：[README.en.md](./README.en.md)
