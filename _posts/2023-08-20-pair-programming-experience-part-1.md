---
title: Trải nghiệm pair programming (phần 1)
tags: programming pair-programming work experience
categories: work
---

Đây là câu chuyện trải nghiệm pair programming của mình, dưới góc nhìn của một người <s>ít tiếp xúc với loài người</s> ít industry experience.

Từ trước đến giờ, mặc dù code qua khá nhiều project - solo nhiều và cũng làm nhóm một vài project, nhưng chưa lần nào mình code trực tiếp một phần nào đó của project trước mặt developer khác để mổ xẻ. Hồi cấp 2 đi thi Tin, mình khá ngại khi có người khác ngồi sau lưng mình và cứ nhìn chằm chằm xem mình code cái gì, trong một phòng thi chỉ có khoảng 10 người nhưng có 1 (hoặc có khi là 2) giám thị ngồi sau lưng mình. Sau này, khi lớn hơn vài năm, trong một dự án làm chung với vài ông anh senior developer qua mạng, mình vẫn không cảm thấy tự tin để có thể share màn hình qua Discord, code để mấy ông review như mấy ổng đã làm.

Thế rồi khi bắt đầu "bước ra ánh sáng", mình đã có 2 lần pair programming, 1 lần là lúc phỏng vấn intern và 1 lần là vừa cách đây vài hôm cho dự án mình đang làm. 

---

Lần phỏng vấn intern này cũng đã qua ngót 2 năm, nên mình cũng không nhớ rõ nhiều chi tiết nữa. Lúc đó là thời điểm có dịch, mình được phỏng vấn online. Trước "mặt" mình là 3 leader phỏng vấn mình. Nền tảng để live code hồi đó hình như là replit.com, hoặc là hackerrank gì đó + share màn hình.

Mình cũng không nhớ bài toán hôm đó là gì, chỉ nhớ chắc chắn là về cây nhị phân. Đại khái mình được cho 1 code base như thế này:

```python
class BinaryTreeNode:
    def __init__(self, value=None):
        self.value = value
        self.left_node = None
        self.right_node = None


class BinaryTree:
    def __init__(self):
        self.root = None

    def add_left(self, node):
        # ...

    def add_right(self, node):
        # ...
```

Thôi thì giả sử bài toán hôm đó là tính độ sâu của node xa gốc nhất trên cây nhị phân (đơn giản hơn phiên bản thật). Lúc đó mình chỉ cần mất khoảng 10 phút suy nghĩ trên nháp. Nhìn chung "văn mẫu" của cấu trúc dữ liệu cây (nói chung) chính là luôn xét trường hợp cây rỗng và xét tất cả các node con của node gốc. Thuật toán rất đơn giản, gọi `get_depth_of_furthest_node(tree)` là hàm trả về độ sâu của node xa gốc nhất trên cây nhị phân, ta có các khả năng:

- `get_depth_of_furthest_node(None) := 0` (trường hợp tầm thường: cây rỗng).
- `get_depth_of_furthest_node(tree, chỉ có node bên trái) := 1 + get_depth_of_furthest_node(cây con bên trái)`
- `get_depth_of_furthest_node(tree, chỉ có node bên phải) := 1 + get_depth_of_furthest_node(cây con bên phải)`.
- `get_depth_of_furthest_node(tree, có cả node bên trái và phải) := 1 + max(get_depth_of_furthest_node(cây con bên trái), get_depth_of_furthest_node(cây con bên phải)`.

Nhìn phát biểu bằng lời của thuật toán có vẻ dài, nhưng với code base như trên, code rất đơn giản:

```python
def get_depth_of_furthest_node(tree: BinaryTreeNode) -> int:
    if tree is None:
        return 0

    return 1 + max(
        get_depth_of_furthest_node(tree.left_node),
        get_depth_of_furthest_node(tree.right_node)
    )
```

Anh leader nhìn qua thì nói code của mình thiếu trường hợp (vì mình không if else 4 trường hợp trong code nữa), nhưng mình code xong thì giải thích lại thuật toán, và anh leader không thấy thiếu trường hợp nữa.

Lúc đó phase live code mất khoảng 30 phút (trong 1 buổi phỏng vấn 60 phút), 10 phút mình nghĩ, 5 phút mình gõ code (code hôm đó khoảng 7-10 dòng), còn lại là review code. Code cho người khác đọc (và cả cho chính mình đọc nữa!) phải rõ ràng, tên biến phải toát lên ý nghĩa, đọc phát hiểu ngay blah blah, y như viết văn vậy. Còn trong nháp mình chỉ viết tên hàm là `f`, tên node là `u` với `v`, vẫn theo phong cách competitive programming, nên chắc vậy mà giờ không nhớ nổi bài toán hôm đó là gì.

Lần live code đó đối với mình hơi nghiêng về test tâm lý, test sự tỉ mỉ hơn là test code, mình đọc đề có thể nghĩ ngay ra ý tưởng sơ bộ (sau đó là trau chuốt thêm edge case), nhưng nếu mình bị rối hay panic thì mình sẽ không xét kỹ tất cả các case, hay có khi code bị ăn lỗi ngay trong lần đầu chạy, vì cần hoàn chỉnh code trước khi báo các leader review code (còn có sự chênh lệch lực lượng giữa 2 bên nữa :P).

Sau buổi hôm đó thì mình pass. Lý thuyết có thể dở nhưng code cấu trúc dữ liệu & giải thuật thì mình tự tin ít dở hơn :P

Phần 1 đến đây là kết thúc, phần 2 sẽ có sau khi mình đánh giá được tình hình hiệu quả ra sao. Không dám hẹn trước lần sau =)))
