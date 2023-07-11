## Two-way data binding trong React là gì?
Trong React, two-way data binding là một khái niệm cho phép đồng bộ hóa dữ liệu giữa phần tử UserInterface(UI) và data source. Có nghĩa là những thay đổi ở trong phần tử UI(như là `input field, checkboxs`,...) cùng được thay đổi theo ở data source, và ngược lại những thay đổi ở trong data source cũng khiến UI thay đổi theo.

Tuy nhiên, có một điều quan trọng là React không hỗ trợ trực tiếp two-way data binding như những framework khác. React chủ yếu tuân theo luồng dữ liệu một chiều(one-way data binding), trong đó dữ liệu chảy từ component cha sang component con của nó thông qua các `props`. Quyền sở hữu của `state` thuộc về component cha. Các component con có đọc và hiển thị dữ liệu-`state` thông qua `props` nhưng không thể trực tiếp thay đổi nó. 

Nếu component con muốn thay đổi dữ liệu-`state` của component cha, nó cần phải giao tiếp với component cha qua các callback functions dưới dạng `props` từ component cha truyền xuống (`handleChange`,`handleClick`,...), Các callback functions này được dùng để update lại `state` của component cha.

>component con update `state` của component cha -> `state` thay đổi -> rerender lại component cha -> component cha chuyển tiếp dữ liệu mới xuống component con qua `props` -> component con nhận dữ liệu mới và hiển thị nội dung mới => dữ liệu vầ UI được đồng bộ => Và đó là ràng buộc dữ liệu 2 chiều(two-way data binding).




