---
title: "Tính toạ độ tâm đường tròn ngoại tiếp tam giác"
tags: geometry
categories: math
---

Nhắc đến đường tròn ngoại tiếp tam giác, trước giờ người ta chỉ quan tâm đến độ dài đường kính, chứ không mấy ai quan tâm đến toạ độ tâm của nó. Hôm nay nhân một buổi nổi hứng làm toán, hình học phẳng (môn mà mình khá dốt), mình đâm đầu vào bài này: [23D - codeforces.com](https://codeforces.com/problemset/problem/23/D).

Đề bài rất đơn giản, nhưng mình sa vào ngộ nhận "tứ giác lồi có 3 cạnh bằng nhau" -> hình thang cân nên tốn mấy tiếng ngồi giải sai :cry: Đọc tutorial mới biết solution chuẩn của bài này cần tính toạ độ tâm đường tròn ngoại tiếp tam giác.

Cơ mà ngồi biến đổi xong, nhìn kết quả đẹp quá tự dưng không muốn code cho xong bài 23D kia nữa...

## Công thức lượng giác

Cho tam giác ABC có toạ độ $A(x_A, y_A)$, $B(x_B, y_B)$, $C(x_C, y_C)$.

Gọi O là tâm đường tròn ngoại tiếp tam giác ABC. Toạ độ của O là:

$$
O(x_O, y_O) = \left(\dfrac{x_A \sin 2A + x_B \sin 2B + x_C \sin 2C}{\sin 2A + \sin 2B + \sin 2C}, \dfrac{y_A \sin 2A + y_B \sin 2B + y_C \sin 2C}{\sin 2A + \sin 2B + \sin 2C}\right)
$$

$\sin 2A$ ở đây là sin của 2 lần góc A.

## "Phá băng" công thức lượng giác

Tính toán lượng giác trên máy tính chắc chắn sẽ có sai số, đôi khi sẽ có sai số khá lớn. Do vậy mình muốn khử bớt lượng giác để hạn chế tính toán sai số nhất có thể.

Như ta đã biết, $\sin 2A = 2 \sin A \cos A$. Áp dụng thêm định lý sin và định lý cos trên tam giác ABC, ta cũng có:

$$
\sin A = \dfrac{2 S_{ABC}}{AB \times AC}
$$

và 

$$
\cos A = \dfrac{AB^2 + AC^2 - BC^2}{2 AB \times AC}
$$

Suy ra

$$
\sin 2A = \dfrac{2 S_{ABC}}{(AB \times AC)^2} \times (AB^2 + AC^2 - BC^2)
= \dfrac{2 S_{ABC}}{(AB \times AC \times BC)^2} \times BC^2 (AB^2 + AC^2 - BC^2)
$$

Ta viết tương tự cho $\sin 2B$ và $\sin 2C$, và kết hợp lại thành các tổng:

$$
\sin 2A + \sin 2B + \sin 2C
= \dfrac{2 S_{ABC}}{(AB \times AC \times BC)^2} \times (\sum BC^2 (AB^2 + AC^2 - BC^2))
$$

$$
x_A \sin 2A + x_B \sin 2B + x_C \sin 2C
= \dfrac{2 S_{ABC}}{(AB \times AC \times BC)^2} \times (\sum x_A BC^2 (AB^2 + AC^2 - BC^2))
$$

$$
y_A \sin 2A + y_B \sin 2B + y_C \sin 2C
= \dfrac{2 S_{ABC}}{(AB \times AC \times BC)^2} \times (\sum y_A BC^2 (AB^2 + AC^2 - BC^2))
$$

Nhìn 3 biểu thức trên, ta có thể dễ dàng rút gọn một khối $\dfrac{2 S_{ABC}}{(AB \times AC \times BC)^2}$ rất lớn, để $x_O$ và $y_O$ chỉ còn rất gọn:

$$
x_O = \dfrac{x_A \sin 2A + x_B \sin 2B + x_C \sin 2C}{\sin 2A + \sin 2B + \sin 2C}
= \dfrac{\sum x_A BC^2 (AB^2 + AC^2 - BC^2)}{\sum BC^2 (AB^2 + AC^2 - BC^2)}
$$

$$
y_O = \dfrac{y_A \sin 2A + y_B \sin 2B + y_C \sin 2C}{\sin 2A + \sin 2B + \sin 2C}
= \dfrac{\sum y_A BC^2 (AB^2 + AC^2 - BC^2)}{\sum BC^2 (AB^2 + AC^2 - BC^2)}
$$

Khi A, B, C có toạ độ là số nguyên, các giá trị $AB^2$, $BC^2$ và $AC^2$ đều là số nguyên. Do vậy khi code, ta không cần phải tính khoảng cách chính xác (bằng phép căn) giữa 2 điểm A, B mà chỉ cần tính bình phương khoảng cách giữa A và B.

Class `Point` thể hiện đối tương điểm trên mặt phẳng toạ độ Oxy:

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def get_sqr_distance(self, other):
        # tính bình phương khoảng cách giữa self và other
        return (self.x - other.x) ** 2 + (self.y - other.y) ** 2
```

Hàm trả về toạ độ tâm ngoại tiếp của tam giác ABC:

```python
def circumcenter_of_triangle(point_A, point_B, point_C):
    AB2 = point_A.get_sqr_distance(point_B)
    BC2 = point_B.get_sqr_distance(point_C)
    CA2 = point_C.get_sqr_distance(point_A)

    # tính giá trị toạ độ của tâm ngoại tiếp tam giác ABC
    numerator = \
        BC2 * (AB2 + AC2 - BC2) + \
        CA2 * (AB2 + BC2 - CA2) + \
        AB2 * (AC2 + BC2 - AB2)

    x_O = (
        point_A.x * BC2 * (AB2 + AC2 - BC2) + \
        point_B.x * CA2 * (AB2 + BC2 - CA2) + \
        point_C.x * AB2 * (AC2 + BC2 - AB2)
    ) / numerator

    y_O = (
        point_A.y * BC2 * (AB2 + AC2 - BC2) + \
        point_B.y * CA2 * (AB2 + BC2 - CA2) + \
        point_C.y * AB2 * (AC2 + BC2 - AB2)
    ) / numerator

    return Point(x_O, y_O)
```

Bạn có thể tuỳ ý tối ưu hay sửa đổi các code ở trên.

## Tham khảo

https://www.vedantu.com/maths/circumcenter-of-triangle
