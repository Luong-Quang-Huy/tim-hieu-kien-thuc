# Hướng dẫn sử dụng Context trong React
## Khi nào nên truyền data bằng Context thay vì bằng props:
Thông thường, ta sẽ truyền *data* từ component cha sang component con thông qua *props*. Nhưng việc chuyển qua các *props* có thể trở nên dài dòng và bất tiện nếu ta phải chuyển chúng qua nhiều component trung gian hoặc nếu nhiều component trong ứng dụng cần cùng một *thông tin-dữ liệu-trạng thái*. *Context* cho phép component cha cung cấp một số *thông tin-dữ liệu-trạng thái* cho bất kỳ phần tử con nào bên dưới nó—bất kể sâu đến đâu mà không cần chuyển *thông tin-dữ liệu-trạng thái* đó một cách rõ ràng thông qua *props* và qua các phần tử trung gian.

## Các bước để sử dụng Context
### Bước 1: Tạo Context
Trước tiên, ta cần tạo *Context*. Ta sẽ cần `export` nó từ một file để các component có thể sử dụng nó:

```js
import {createContext} from 'react';

export const ColorContext = createContext('red');
```
Đối số duy nhất mà  **createContext** nhận vào gọi là *default value*. Ở đâu là 1 chuỗi *'red'*, nhưng ta có thể truyền bất kể loại giá trị nào(*kể cả object hay hàm*). Bất cứ component nào mà sử dụng *ColorContext* này, component đó sẽ tìm đến  ColorContext Provider gần nó nhất để lấy giá trị mà nó cung cấp, nếu không có tìm thấy **Provider** nào thì sẽ sử dụng giá trị mặc định đã truyền vào **createContext** (chuỗi *'red'*).

### Bước 2: Sử dụng Context
import **useContext** Hook trong React và Context ta vừa tạo (giả sử lưu trong file tên là `ColorContext.js`) ở nơi ta muốn sử dụng:
```js
import {useContext} from 'react';
import {ColorContext} from './ColorContext.js';
```

Giả sử có các component sau:
```js
//trong file Section.jsx
export default function Section({chilren}){
    return <section>{chilren}</section>;
}
//trong file Paragraph.jsx
export function Paragraph({color,children}){
    return <p
        style={{"color": color}}
    >{chilren}</p>;
}

```
Compponent Paragraph này nhận giá trị color thông qua props bằng cách thông thường.

Giả sử bây giờ ta có nhiều Paragraph trong một Section cùng lấy giá trị color giống nhau:
```js
<Section>
    <Paragraph color={"green"}>Ahihi</Paragraph>
    <Paragraph color={"green"}>Một hai ba</Paragraph>
    <Paragraph color={"green"}>Bốn năm sáu</Paragraph>
</Section>
<Section>
    <Paragraph color={"blue"}>Bảy tám chín</Paragraph>
    <Paragraph color={"blue"}>Mười J Q</Paragraph>
    <Paragraph color={"blue"}>Yasuo thông thạo 7</Paragraph>
</Section>
``` 

Vì mọi Paragraph trong cùng một Section đều có màu giống nhau nên trường hợp này thay vì truyền color cho từng Paragraph một thì nên truyền color cho Section rồi sử dụng Context để cung cấp giá trị đó cho các component con.

Bây giờ Paragraph không còn nhận color thông qua prop nữa mà sử dụng Context nên ta chỉnh sửa lại định nghĩa của Paragraph: 
```js
//Trong file Paragraph.jsx
import {useContext} from 'react';
import {ColorContext} from './ColorContext.js';

export default function Paragraph({children}){
    const color = useContext(ColorContext);
    return <p
         style={{"color": color}}
    >{chilren}</p>;
}
```
***useContext:***
Là một Hook của React. Ở đây nói với React rằng Paragraph component muốn đọc `ColorContext`.

Section giờ cần nhận vào color thông qua props nên ta chỉnh sửa lại định nghĩa Section:
```js
//Trong file Section.jsx
export default function Section({color, chilren}){
    return <section>{chilren}</section>;
}
```

Giờ JSX sẽ trông như sau: 
```js
<Section color={'green'}>
    <Paragraph>Ahihi</Paragraph>
    <Paragraph>Một hai ba</Paragraph>
    <Paragraph>Bốn năm sáu</Paragraph>
</Section>
<Section color={'blue'}>
    <Paragraph>Bảy tám chín</Paragraph>
    <Paragraph>Mười J Q</Paragraph>
    <Paragraph>Yasuo thông thạo 7</Paragraph>
</Section>
```
Tuy nhiên bây giờ khi run app thì chỉ thấy các đoạn văn bản toàn là màu đỏ, mặc dù Paragraph đã sử dụng `useContext` nhưng ta chưa cung cấp(*provide*) *Context* cho nó. Hiện tại React đang sử dụng giá trị mặc định(*default value*) mà ta đã truyền vào cho tham số của `createContext` ở bước 1, ở VD này là chuỗi 'red' nên useContet(ColorContext) return 'red' nên mọi dòng chữ đều có màu đỏ. Bây giờ mỗi Section cần cung cấp *Context* của riêng nó, ta đến bước tiếp theo.

### Bước 3: Cung cấp Context
Bọc các phần tử con(*chilren*) của Section trong một *context provider* để cung cấp `ColorContext` cho chúng:
```js
//Trong file Section.jsx
export default function Section({color, chilren}){
    return 
    (<section>
        <ColorContext.Provider value={color}>
            {chilren}
        <ColorContext.Provider>
    </section>);
}
```
Từ giờ khi bất kì phần tử con nào bên dưới Section dù bất kể là sâu đến đâu đều có thể đọc được giá trị mà `ColorContext.Provider` cung cấp,  `useContext(ColorContext)` bên trong phần tử con đó sẽ `return` về giá trị được cung cấp bởi **ColorContext Provider** gần nó nhất ở phía trên trong UI tree. 


