# BÀI LÀM PHA 1 

- **Họ tên:** Vũ Lê Huy
- **Lớp:** CNTT1
- **Link repository Pha1:**

> Pha 1 (70 phút đầu, KHÔNG dùng AI). Viết xong, kéo thả file này vào repository Pha1 trước mốc giờ giám thị công bố.

## 4.1. Bảo vệ dữ liệu và chống ảo giác

### a) Ghi chú đã ẩn danh hoá

**GHI CHU DA AN DANH**

```text
[Khán giả Nguyên Văn A],[SDT:079xxxxxxxx],[email nguyenvana@gmail.com],[Đặt 1 ít vé gói SVT trận đấu bán kết cúp sao vàng ngày 1x/1x/2xxx cho 3 người. Khán giả đã chuyển 1 số tiền lớn và yêu cầu 3 ghế sát nhau]
```

### b) Hai câu lệnh chống ảo giác

**CAU LENH**

```text
 Chỉ sử dụng thông tin có trong file chính sách đính kèm để trả lời, tuyệt đối không suy diễn hay dùng kiến thức bên ngoài; nếu tài liệu không đề cập đến trường hợp này, hãy trả lời không tìm thấy tài liệu. Bắt buộc phải chỉ rõ số mục và trang chứa thông tin đó trong file đính kèm. 
```

## 4.2. Trích xuất tin nhắn đặt vé ra JSON

**PROMPT**

```text
C - Context:
Bạn là trợ lý trích xuất dữ liệu đặt vé sự kiện thể thao từ tin nhắn khách hàng. Dữ liệu đầu vào gồm tin nhắn mẫu và tin nhắn thực tế cần chuyển đổi sang định dạng JSON có cấu trúc gồm đúng 6 trường: "ma_goi", "ngay_su_kien", "nguoi_lon", "tre_em", "loai_ve", "ghi_chu".

Bảng mã gói:
- SVT – Sân Vận Động: vé trận đấu
- FZ – Fan Zone: khu cổ vũ
- VIP – Khán đài VIP

R - Request:
Chuyển đổi các tin nhắn cần xử lý (TN1, TN2, TN3) thành một mảng JSON gồm đúng 3 đối tượng theo đúng thứ tự TN1, TN2, TN3, giữ nguyên tên và thứ tự 6 trường như tin nhắn mẫu.

E - Explanation:
Áp dụng nghiêm ngặt 5 quy tắc chuẩn hoá sau cho mọi tin nhắn:
1. ma_goi lấy theo bảng mã gói
2. ngay_su_kien dạng YYYY-MM-DD; tin nhắn không ghi năm thì lấy năm 2026; không có ngày cụ thể thì null.
3. Khách không nhắc trẻ em thì tre_em là 0 và mọi khách là người lớn.
4. "loai_ve" chỉ nhận giá trị "thuong", "vip" hoặc null.
5. ghi_chu chỉ ghi yêu cầu đặc biệt về chỗ ngồi, hỗ trợ di chuyển hoặc ăn uống; không có thì null.


Dưới đây là 2 tin nhắn mẫu đã xử lý đúng (Few-shot):

Tin nhắn mẫu 1:
Đặt giúp mình 2 vé VIP trận chung kết ngày 15/11/2026, 2 người lớn và 1 bé. Cần chỗ ngồi cạnh lối đi.
Kết quả đúng:
{"ma_goi": "VIP", "ngay_su_kien": "2026-11-15", "nguoi_lon": 2, "tre_em": 1, "loai_ve": "vip", "ghi_chu": "chỗ ngồi cạnh lối đi"}

Tin nhắn mẫu 2:
Cho mình một vé Fan Zone, ngày nào cũng được, mình đi một mình.
Kết quả đúng:
{"ma_goi": "FZ", "ngay_su_kien": null, "nguoi_lon": 1, "tre_em": 0, "loai_ve": "thuong", "ghi_chu": null}

A - Action:
Đọc từng tin nhắn bên trong thẻ <tin_nhan> theo đúng thứ tự TN1, TN2, TN3, chuẩn hoá dữ liệu theo 5 quy tắc và xuất ra một mảng JSON chứa 3 đối tượng tương ứng.

T - Tone:
Chính xác,ng thêm bớt thông tin suy diễn ngoài dữ liệu và quy tắc đã cho, ngắn gọn súc tích

E - Extras:
Chỉ trả về duy nhất một mảng JSON hợp lệ, không lời chào hỏi, không giải thích, không dùng markdown code block hay văn bản thừa nào khác.

<tin_nhan>
TN1: Mình muốn đặt gói Sân Vận Động trận tứ kết ngày 20/12 nha, 2 người lớn với 1 bé 8 tuổi, vé thường.

TN2: Cho 3 người đi Fan Zone trận ngày 05/01/2027 ạ. Một người cần hỗ trợ xe lăn.

TN3: Trận chung kết Cúp Sao Vàng tháng 12 còn vé không ạ? Nhà em 4 người lớn, có một người khiếm thị cần hỗ trợ tìm chỗ, lấy vé VIP.
</tin_nhan>
```

## 4.3. Báo giá vé bằng Chain-of-Thought

### a) Prompt

**PROMPT**

```text
Bạn là trọ lý ảo tính giá vé. Hãy dựa vào dữ liệu tính tổng tiền giá vé theo đúng quy tắc
<dữ_liệu>
BẢNG GIÁ VÉ (đồng/người lớn)
SVT – Sân Vận Động: 1.200.000
FZ – Fan Zone: 800.000
VIP – Khán đài VIP: 2.500.000

QUY TẮC TÍNH GIÁ
1. Trẻ em từ 5 đến 11 tuổi tính 70% giá người lớn. Trẻ dưới 5 tuổi miễn phí.
2. Nhóm từ 5 người lớn trở lên được giảm 5% trên tiền vé (tiền người lớn + tiền trẻ em).
3. Phụ thu khu ghế ưu tiên: 300.000 đồng/người. Phụ thu không được giảm giá.
4. Tổng tiền = tiền vé sau giảm + phụ thu.

YÊU CẦU BÁO GIÁ
Nhóm cổ động viên đi xem trận bán kết, mua gói SVT, gồm 5 người lớn, 2 trẻ em (8 tuổi và 10 tuổi) và 1 bé 3 tuổi. Có 1 người lớn chọn khu ghế ưu tiên.
</dữ_liệu>
Hãy suy luận từng bước rõ ràng và mỗi bước ghi rõ từng phép tính 
tiền người lớn → tiền trẻ em → tiền vé → giảm giá nhóm → phụ thu → tổng tiền 
Dòng cuối có dạng [RESULT: <tổng tiền> đồng].

```

## 4.4. Dây chuyền xử lý khiếu nại (Prompt Chaining)

### a) Prompt Trạm 1

**PROMPT**

```text
Bạn là trợ lý phân tích email khiếu nại của khán giả sự kiện Cúp Sao Vàng.
Hãy đọc email khiếu nại bên trong thẻ <email_khieu_nai> và trích xuất thông tin ra đúng một đối tượng JSON duy nhất gồm 4 trường sau:
- "loai_su_co": một trong các giá trị "tran_gio" | "sai_khu_vuc" | "nhan_vien" | "khac"
- "so_khach": số nguyên (tổng số khách gặp sự cố)
- "so_hiep_bi_anh_huong": số nguyên hoặc null
- "tom_tat": chuỗi tóm tắt ngắn gọn nội dung khiếu nại, tối đa 20 từ

Quy tắc:
- Chỉ dựa vào nội dung trong email, không suy diễn thêm.
- Chỉ trả về duy nhất một đối tượng JSON hợp lệ, không chào hỏi, không giải thích, không dùng markdown code block hay ký tự thừa nào khác.

<email_khieu_nai>
Tôi là khán giả mua vé gói SVT trận Cúp Sao Vàng ngày 02/11/2026, đi 2 người lớn. Ban tổ chức cam kết khu khán đài có mái che nhưng trong 2 hiệp đầu chúng tôi bị xếp sang khu không mái, trời mưa và ghế ướt. Nhân viên soát vé rất nhiệt tình, tôi không có gì phàn nàn về nhân viên. Tôi đề nghị ban tổ chức giải quyết thỏa đáng.
</email_khieu_nai>
```

### b) Hai điều cần kiểm tra ở JSON đầu ra của Trạm 1

1. (Viết câu trả lời vào đây – xoá dòng này)

2.

### c) Câu lệnh viết tiếp

**CAU LENH**

```text
(Viết câu lệnh vào đây – xoá dòng này)
```

## 4.5. Prompt chấm bài theo Rubric

**PROMPT**

```text
(Viết prompt vào đây – xoá dòng này)
```

## 4.6. Hoàn thiện System Prompt

> Phần Vai trò và Chuẩn đầu ra đã cho sẵn. Viết tiếp phần Quy tắc ràng buộc và Xử lý ngoại lệ.

```text
[Vai trò]
Bạn là "Trợ lý Cúp Sao Vàng", nhân viên tư vấn vé và dịch vụ trên website của giải bóng đá Cúp Sao Vàng, giúp khán giả chọn và đặt một trong 3 gói: SVT (Sân Vận Động – vé trận đấu), FZ (Fan Zone – khu cổ vũ), VIP (Khán đài VIP).

[Quy tắc ràng buộc]
1. (Viết quy tắc 1 – xoá dòng này)
2.
3.

[Chuẩn đầu ra]
Trả lời bằng tiếng Việt, lịch sự, tối đa 120 từ. Giá vé trình bày dạng gạch đầu dòng. Kết thúc bằng một câu hỏi về nhu cầu tiếp theo của khách.

[Xử lý ngoại lệ]
- Khách hỏi ngoài phạm vi: (Viết cách trả lời – xoá dòng này)
- Khách cung cấp thiếu thông tin:
```
