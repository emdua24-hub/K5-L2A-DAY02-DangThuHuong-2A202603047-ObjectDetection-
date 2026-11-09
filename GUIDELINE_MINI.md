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
| `drive_005.jpg` | Xe sedan màu bạc ở góc dưới bên phải, bị rìa phải ảnh cắt mất phần đầu xe. Phần còn lại vẫn thấy rõ cửa sau, bánh sau, cốp và dáng thân thấp. | Gán `car` | Theo quy tắc "bị cắt bởi rìa ảnh": phần nhìn thấy đủ để nhận ra lớp. Dáng thân thấp, có cốp kiểu sedan, không có thùng hàng hay thân hộp cao, nên thuộc `car` (sedan nằm trong "Gán khi" của `car`). Box kéo sát đến rìa phải ảnh, chỉ ôm phần nằm trong khung hình. |
| `drive_006.jpg` | Xe màu trắng ở mép trái ảnh, bị cắt gần hết, chỉ còn thấy phần đầu xe: nắp capo cong, đèn pha và cản trước. | Gán `car` | Theo quy tắc "bị cắt bởi rìa ảnh": tuy chỉ còn phần đầu nhưng capo thấp, cong và cụm đèn pha nhỏ là đặc điểm của xe con, không phải đầu phẳng và cao của `van`/`bus` hay cabin vuông của `truck`. Vì nhận ra được lớp nên vẫn gán; box kéo sát đến rìa trái và chỉ ôm phần đầu xe nhìn thấy. |
| `drive_009.jpg` | Xe con màu xám xanh ở mép phải ảnh, nằm phía sau đầu xe buýt đỏ trắng: bị xe buýt che phần lớn thân bên trái và bị rìa phải ảnh cắt phần còn lại. Chỉ thấy nóc, kính và một phần thân xe. | Gán `car`, tách riêng box với xe buýt | Theo quy tắc "vật thể bị che khuất" và "bị cắt bởi rìa ảnh": phần nóc và kính nhìn thấy đủ nhận ra là xe con nên vẫn gán `car`. Box chỉ ôm phần xe con đang nhìn thấy, không vẽ phỏng đoán phần bị xe buýt che, và không gộp với box của xe buýt (mỗi xe một box riêng). |
