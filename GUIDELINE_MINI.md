# Mini guideline — bài làm cá nhân

> Điền nội dung này trước khi bắt đầu gán nhãn. Giữ lại các quyết định sau khi self-QC.

## Định nghĩa lớp

| Lớp | Định nghĩa của bạn | Ví dụ/điểm phân biệt |
| --- | --- | --- |
| `car` | Xe chở người cỡ nhỏ (thường 4–7 chỗ): sedan, hatchback, SUV, taxi và xe bán tải. | Kia Morning, Toyota Vios, Fortuner, taxi, Ford Ranger. Bán tải có thùng hở phía sau vẫn là `car`, không phải `truck`. |
| `truck` | Xe chuyên chở hàng, có cabin lái tách riêng với thùng hàng hoặc rơ-moóc phía sau. | Xe tải thùng, xe ben, xe bồn, đầu kéo container. Cabin vuông đứng riêng, thùng hàng to và thường cao hơn cabin. |
| `bus` | Xe chở khách cỡ lớn/vừa, thân dài dạng hộp, nhiều hàng cửa sổ, có cửa lên xuống cho hành khách. | Xe buýt thành phố, xe khách, xe giường nằm, minibus 29 chỗ (Hyundai County, Thaco). Dài hơn hẳn van, mũi xe gần như phẳng. |
| `van` | Xe thân liền một khối (cabin và khoang sau chung một khối), chở hàng hoặc chở người, cỡ giữa `car` và `bus`. | Ford Transit, Hyundai Solati, Toyota Hiace, Suzuki Blind Van, minivan. Ngắn hơn bus; cao và vuông hơn car. Xe 16 chỗ dạng Transit/Solati là `van`. |

## Quy tắc chung

- Box bám sát vật thể nghĩa là: 4 cạnh box chạm đúng mép ngoài cùng của phần xe nhìn thấy được (kể cả gương chiếu hậu, thùng hàng), không chừa khoảng trống thừa, không cắt mất phần nào của xe, không bao gồm bóng đổ dưới đất.
- Vật thể bị che khuất: vẫn gán box nếu phần nhìn thấy đủ để nhận ra đúng lớp; box chỉ ôm phần đang nhìn thấy, không vẽ phỏng đoán phần bị che. Nếu bị che quá nhiều đến mức không phân biệt được lớp thì bỏ qua.
- Vật thể bị cắt bởi rìa ảnh: vẫn gán box nếu phần còn lại đủ để nhận ra lớp; box kéo sát đến rìa ảnh và chỉ bao phần nằm trong khung hình.
- Vật thể rất nhỏ/mơ hồ: nếu quá nhỏ hoặc quá mờ để xác định là `car`, `truck`, `bus` hay `van` thì không gán, không đoán bừa; nếu vẫn nhận ra lớp chắc chắn thì gán bình thường.

## Ghi lại quyết định cho ít nhất ba ca mơ hồ

| Ảnh | Tình huống | Quyết định cuối | Lý do theo schema |
| --- | --- | --- | --- |
| `drive_012.jpg` | Vật thể màu trắng ở xa, ảnh mờ nhòe (motion blur), phần lớn thân xe bị cắt bởi rìa ảnh. Có dáng giống xe nhưng không thấy rõ đầu, đuôi hay kích thước. | Không gán nhãn | Theo quy tắc "vật thể rất nhỏ/mơ hồ": phần nhìn thấy không đủ để xác định chắc chắn là `car`, `van` hay `truck`. Gán đại sẽ tạo nhãn sai lớp, nên bỏ qua thay vì đoán. |
| `drive_020.jpg` | Xe màu tối bị rìa phải ảnh cắt mất phần lớn, chỉ còn thấy phần đuôi: cụm đèn hậu, cản sau và cốp thấp kiểu sedan. | Gán `car` | Theo quy tắc "bị cắt bởi rìa ảnh": phần còn lại vẫn đủ nhận ra lớp. Đuôi thấp, có cốp và đèn hậu kiểu sedan thuộc `car` (sedan nằm trong "Gán khi" của `car`), không có thùng hàng hay thân hộp cao như `van`/`truck`. Box kéo sát đến rìa phải ảnh, chỉ ôm phần nhìn thấy. |
| `drive_021.jpg` | Xe sedan màu bạc đang đỗ, bị người đi bộ và cột che khuất một phần thân xe. | Gán `car` | Theo quy tắc "vật thể bị che khuất": phần nhìn thấy (nóc, kính, bánh xe, dáng sedan thấp) đủ để nhận ra `car`. Xe đang đỗ vẫn gán theo loại xe vì lớp mô tả loại vật thể, không mô tả trạng thái. Box chỉ ôm phần đang nhìn thấy, không vẽ phỏng đoán phần bị che. |
