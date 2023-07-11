# State: trí nhớ của các component

### Mổ sẻ useState Hook

Khi ta gọi `useState`, ta nói với React rằng ta muốn component này ghi nhớ điều gì đó giữa mỗi lần Render: 

```js
const [count, setCount] = useState(0);
```
Ở vd này, ta muốn React ghi nhớ `count`.

Đối số truyền vào duy nhất cho `useState` là giá trị khởi tạo cho biến `state`. Ở vd này, giá trị khởi tạo của `count` được đặt là 0 với `useState(0)`.

Mỗi lần component này render, `useState` trả về một mảng chứa hai giá trị:

1. **state value**(`count`) với giá trị đã lưu trước đó.
2. **state setter function**(`setCount`) - phương thúc dùng để uppdate **state variable**(`count`) và kích hoạt cơ chế **rerender** của React.

### Cụ thể hon như sau: 

```js
const [count, setCount] = useState(0);

const handleIncrease = () => {
    setCount(count + 1);
};
```
1.Khi component được render lần đầu tiên. Bởi vì ta đã set 0 cho `useState` làm giá trị khởi tạo cho `count`, `useState(0)` sẽ trả về  `[0, setCount]`. React ghi nhớ 0 hiện đang là giá trị mới nhất của **state value**.

2.Khi `state` được uppdate, chẳng hạn *user* bấm vào nút `increase` -> gọi `handleIncrease()` -> `count` từ 0 được uppdate lên 1. React ghi nhớ trạng thái `count` là 1 và kích hoạt render lại component.

3.ở lần render thứ 2, Khi chạy lại Compnent để lấy JSX. Khi đến đoạn chạy `useState` ở đoạn:

```js
    const [count, setCount] = useState(0);
``` 
React vẫn nhìn thấy `useState(0)`, nhưng React nhớ rằng **state value** của count là 1 nên thay vì trả về `[0, setCount]` nó trả về `[1,setCount]`.

4.Và mỗi lần bấm nút `increse` về sau sẽ tương tự.

### State là cô lâp và riêng tư của mỗi component

**State** là local với mỗi thực thể component trong **DOM**. Nói cách khác, nếu render cùng một component ở 2 vị trí khác nhau, mỗi component sẽ có **State** riêng biệt. Thay đổi **State** của một trong hai component không ảnh hưởng gì đến **State** của component còn lại. **State** ở mỗi component là độc lập.

Điều khác biệt của **State**  so với các biến thông thường khác mà ta khai báo trong **component** đó là không bị ràng buộc với **component function call** (cái hàm được gọi để trả về JSX) hay là nằm ở một nơi nào đó ở trong code, mà nó được lưu trữ và phân bổ cho một vị trí cụ thể trên màn hình. Khi component không còn tồn tại trong **DOM** nữa thì **State** của nó cũng không còn. 


## Những sai lầm thường gặp khi sử dụng State 

- ### Thay đổi trực tiếp biến State
React State được xem là bất biến và không thể thay đổi trực tiếp bằng cách gán. Thay vào đó, ta phải sử dụng **setter function** mà useState trả về kèm với **state**  đó. Ngoài ra, khi update **state** có giá trị dạng Object hay Array, ta cần phải truyền vào **setter function** một thực thể Object hay Array mới thay vì nếu chỉ thay đổi giá trị thuộc tính của Object hay thay đổi một vài giá trị trong Array. 

```js
//vd với Object
const [clientIn4, setClientIn4] = useState({id: 1, name: "huy"});
const handleNameChange = (name) => {
    cilentIn4.name = name;
    setClientIn4(clientIn4);//không có tác dụng vì thực ra giá trị truyền vào setter function không có gì thay đổi so với state trc đó-vẫn cùng trỏ đến 1 object -> state không được uppdate.
}
```

Thay vào đó truyền vào **setter function** một thực thể khác

```js
const [clientIn4, setClientIn4] = useState({id: 1, name: "huy"});
const handleNameChange = (name) => {
   setClientIn4({
    ...clientIn4,
    "name": name 
   });
}
```

- ### Sử dụng biến state ngay sau khi set bằng setter function, ngộ nhận biến state đã mang giá trị mới.

```js
const [clientIn4, setClientIn4] = useState({id: 1, name: "huy"});
const handleNameChange = (name) => {
   setClientIn4({
    ...clientIn4,
    "name": name 
   });
   console.log(clientIn4.name);// vẫn là giá trị cũ. 
}
```
Vì `clientIn4` cũng chỉ là một biến thông thường dùng để lưu trữ giá trị hiện tại của `state` ở mỗi lần render mà thôi chứ nó không phải là `state`. Giá trị mà clientIn4 đang mang là giá trị của `state` ở lần render vừa xong chứ không phải là giá trị mới nhất.Ở thời điểm `console.log` được chạy thì hàm update cho `clientIn4` vẫn còn ngồi trong hàng đợi chờ cho hàm `handleNameCChange` chạy xong mới bắt đầu được xử lí do cơ chế **batching**. 