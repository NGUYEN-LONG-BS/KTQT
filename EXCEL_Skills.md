# Hướng dẫn cá nhân

## Định dạng kỳ
- Kỳ tháng ghi dạng 2026M01, kỳ quý ghi dạng 2026Q01 (không dùng T01/2026 hay Quý I/2026)

## Ngôn ngữ
- Luôn trình bày trong khung chat bằng tiếng Việt

## Định dạng công thức
- Công thức dài (SUMIFS, XLOOKUP, IF lồng nhau…) viết xuống dòng: tên hàm và ngoặc mở ở dòng đầu, mỗi đối số hoặc cặp điều kiện (vùng, điều kiện) nằm trên một dòng, ngoặc đóng và phép tính phía sau (ví dụ /10^6) ở dòng cuối.
- Không thụt đầu dòng; nếu thật sự cần thụt (ví dụ hàm lồng nhau) thì dùng đúng 4 dấu cách.
- Ví dụ:

```
=SUMIFS(
Table[Amount],
Table[Cost_Center],$A89,
Table[Year],C$87,
Table[Month],C$88
)/10^6
```


## 6. Công thức mẫu (trích từ BT_Dong_Lam)

> Đổi tiền tố `DLAM_` thành `DLOC_` hoặc `KNGA_` để dùng cho trạm khác.
> Dòng 4 = Year, dòng 5 = Month, cột H:S = tháng 1–12.
> Phần lớn là công thức mảng (dynamic array). Các ô có FILTER, UNIQUE hoặc SORT sẽ tràn xuống các dòng bên dưới.

### 6.1. Tiêu đề và cột tổng hợp

Tiêu đề sheet:
```
="TỔNG CHI PHÍ  - "&UPPER(MD_MA_DON_VI[TEN_DON_VI_DLAM])
```

Tiêu đề cột TB lũy kế:
```
="TB LK "&MD_DATE_INFO[This period - month]&"T"
```

Tổng mục:
```
=SUM(H9:H14)
```

TB lũy kế đến kỳ hiện hành:
```
=SUMPRODUCT(
H8:S8,
--(H$5:S$5<=MD_DATE_INFO[This period - month])
)/MD_DATE_INFO[This period - month]
```

TB lũy kế, chỉ tính các tháng có phát sinh:
```
=IFERROR(SUMPRODUCT(
H178:S178,
--(H$5:S$5<=MD_DATE_INFO[This period - month])
)/SUMPRODUCT(
--(H$5:S$5<=MD_DATE_INFO[This period - month]),
--(H178:S178>0)
),0)
```

TB 12 tháng:
```
=SUM(H8:S8)/12
```

Lũy kế đến kỳ hiện hành:
```
=SUMPRODUCT(
H8:S8,
--(H$5:S$5<=MD_DATE_INFO[This period - month])
)
```

Tổng 12 tháng:
```
=SUM(H8:S8)
```

Số thứ tự:
```
=ROW()-ROW(A$267)
```

### 6.2. Danh sách mã hàng tự động (MB51 theo lệnh SX)

Mã NVL đưa vào phân loại chai, gồm mã đầu 15/16 và mã gia công:
```
=SORT(UNIQUE(FILTER(
DLAM_data_sx_PHAN_LOAI_CHAI[[Material]:[Material_Description]],
(DLAM_data_sx_PHAN_LOAI_CHAI[Material]<>"")*
((--(LEFT(DLAM_data_sx_PHAN_LOAI_CHAI[Material],2)="15"))+
(--(LEFT(DLAM_data_sx_PHAN_LOAI_CHAI[Material],2)="16"))+
(--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Material],{"810002615","810002616"},0))))*
(DLAM_data_sx_PHAN_LOAI_CHAI[Movement_Type]="261")
)),1,1)
```

Dùng lại danh sách đã tràn:
```
=ANCHORARRAY($B$9)
```

Mã TP sau phân loại, MvT 101:
```
=SORT(UNIQUE(FILTER(
DLAM_data_sx_PHAN_LOAI_CHAI[[Material]:[Material_Description]],
(DLAM_data_sx_PHAN_LOAI_CHAI[Material]<>"")*
(DLAM_data_sx_PHAN_LOAI_CHAI[Movement_Type]="101"),"---")),1,1)
```

Cột phụ: chỉ mã NVL đầu vào, dùng làm mẫu số cho cơ cấu:
```
=SORT(UNIQUE(FILTER(
DLAM_data_sx_PHAN_LOAI_CHAI[Material],
(DLAM_data_sx_PHAN_LOAI_CHAI[Material]<>"")*
((--(LEFT(DLAM_data_sx_PHAN_LOAI_CHAI[Material],2)="15"))+
(--(LEFT(DLAM_data_sx_PHAN_LOAI_CHAI[Material],2)="16"))+
(--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Material],{"810002615","810002616"},0))))*
(DLAM_data_sx_PHAN_LOAI_CHAI[Movement_Type]="261")
)),1,1)
```

Mã NVL phân loại nắp. Băm chai (`BAM_VAY`) và dây đai (`DAY_DAI`) dùng tương tự:
```
=SORT(UNIQUE(FILTER(
DLAM_data_sx_PHAN_LOAI_NAP[[Material]:[Material_Description]],
(DLAM_data_sx_PHAN_LOAI_NAP[Material]<>"")*
((--(LEFT(DLAM_data_sx_PHAN_LOAI_NAP[Material],2)="15"))+
(--(LEFT(DLAM_data_sx_PHAN_LOAI_NAP[Material],2)="16")))*
(DLAM_data_sx_PHAN_LOAI_NAP[Movement_Type]="261"),"---")),1,1)
```

Cột phụ phân loại nắp:
```
=SORT(UNIQUE(FILTER(
DLAM_data_sx_PHAN_LOAI_NAP[Material],
(DLAM_data_sx_PHAN_LOAI_NAP[Material]<>"")*
((--(LEFT(DLAM_data_sx_PHAN_LOAI_NAP[Material],2)="15"))+
(--(LEFT(DLAM_data_sx_PHAN_LOAI_NAP[Material],2)="16")))*
(DLAM_data_sx_PHAN_LOAI_NAP[Movement_Type]="261"),"---")),1,1)
```

Cột phụ băm chai, không có giá trị dự phòng "---":
```
=SORT(UNIQUE(FILTER(
DLAM_data_sx_BAM_VAY[Material],
(DLAM_data_sx_BAM_VAY[Material]<>"")*
((--(LEFT(DLAM_data_sx_BAM_VAY[Material],2)="15"))+
(--(LEFT(DLAM_data_sx_BAM_VAY[Material],2)="16")))*
(DLAM_data_sx_BAM_VAY[Movement_Type]="261")
)),1,1)
```

### 6.3. NVL đưa vào sản xuất (MB51, MvT 261/262)

Số lượng, đơn vị Tấn:
```
=-SUMPRODUCT(
DLAM_MB51[Qty_in_Un_of_Entry],
--(DLAM_MB51[Material]=$B9),
--(ISNUMBER(MATCH(DLAM_MB51[Movement_Type],{"261";"262"},0))),
--(DLAM_MB51[Month]=H$5),
--(DLAM_MB51[Year]=H$4)
)/10^3
```

Giá trị, đơn vị Triệu:
```
=-SUMPRODUCT(
DLAM_MB51[Amount_in_LC],
--(DLAM_MB51[Material]=$B16),
--(ISNUMBER(MATCH(DLAM_MB51[Movement_Type],{"261";"262"},0))),
--(DLAM_MB51[Month]=H$5),
--(DLAM_MB51[Year]=H$4)
)/10^6
```

### 6.4. Cơ cấu thành phẩm và hao hụt

Cơ cấu TP (%) = SL TP 101/102 / SL NVL 261/262:
```
=IFERROR(-SUMPRODUCT(
DLAM_data_sx_PHAN_LOAI_CHAI[Tong_So_Luong],
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Material],$B23,0)),
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Movement_Type],{"101";"102"},0)),
--(DLAM_data_sx_PHAN_LOAI_CHAI[Year]=H$4),
--(DLAM_data_sx_PHAN_LOAI_CHAI[Month]=H$5)
)/SUMPRODUCT(
DLAM_data_sx_PHAN_LOAI_CHAI[Tong_So_Luong],
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Material],ANCHORARRAY($E$23),0)),
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Movement_Type],{"261";"262"},0)),
--(DLAM_data_sx_PHAN_LOAI_CHAI[Year]=H$4),
--(DLAM_data_sx_PHAN_LOAI_CHAI[Month]=H$5)
),)
```

Tỷ lệ hao hụt mất kg:
```
=IF(SUM(H23:H49)=0,0,1-SUM(H23:H49))
```

Giá trị hao hụt:
```
=+H623*H629
```

### 6.5. Thành phẩm sau sản xuất (MB51, MvT 101/102)

Phân loại chai:
```
=SUMPRODUCT(
DLAM_data_sx_PHAN_LOAI_CHAI[Tong_So_Luong],
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Material],$B53,0)),
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_CHAI[Movement_Type],{"101";"102"},0)),
--(DLAM_data_sx_PHAN_LOAI_CHAI[Year]=H$4),
--(DLAM_data_sx_PHAN_LOAI_CHAI[Month]=H$5)
)/10^3
```

Phân loại nắp, có chặn mã trống hoặc "---":
```
=IF(OR($B159="",$B159="---"),0,SUMPRODUCT(
DLAM_data_sx_PHAN_LOAI_NAP[Tong_So_Luong],
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_NAP[Material],$B159,0)),
--ISNUMBER(MATCH(DLAM_data_sx_PHAN_LOAI_NAP[Movement_Type],{"101";"102"},0)),
--(DLAM_data_sx_PHAN_LOAI_NAP[Year]=H$4),
--(DLAM_data_sx_PHAN_LOAI_NAP[Month]=H$5)
))/10^3
```

Băm chai:
```
=IF(OR($B202="",$B202="---"),0,SUMPRODUCT(
DLAM_data_sx_BAM_VAY[Tong_So_Luong],
--(DLAM_data_sx_BAM_VAY[Material]=$B202),
--ISNUMBER(MATCH(DLAM_data_sx_BAM_VAY[Movement_Type],{"101";"102"},0)),
--(DLAM_data_sx_BAM_VAY[Year]=H$4),
--(DLAM_data_sx_BAM_VAY[Month]=H$5)
))/10^3
```

### 6.6. Xuất bán và doanh thu (ZFIR0012)

Số lượng xuất bán, chỉ tính khi có SX trong kỳ:
```
=IF(H53=0,0,SUMPRODUCT(
DLAM_ZFIR0012[Số_lượng],
--(DLAM_ZFIR0012[Mã_hàng_(mã_mới)]=$B82),
--(DLAM_ZFIR0012[Year]=H$4),
--(DLAM_ZFIR0012[Month]=H$5)
))/10^3
```

Doanh thu theo công đoạn:
```
=IF(H53=0,0,SUMPRODUCT(
DLAM_ZFIR0012[Thành_tiền(chưa_VAT)],
--(DLAM_ZFIR0012[Mã_hàng_(mã_mới)]=$B111),
--(DLAM_ZFIR0012[Year]=H$4),
--(DLAM_ZFIR0012[Month]=H$5)
))/10^6
```

Doanh thu theo mã hàng:
```
=SUMPRODUCT(
DLAM_ZFIR0012[Thành_tiền(chưa_VAT)],
--(DLAM_ZFIR0012[Mã_hàng_(mã_mới)]=$B826),
--(DLAM_ZFIR0012[Year]=H$4),
--(DLAM_ZFIR0012[Month]=H$5)
)/10^6
```

Tên hàng:
```
=INDEX(DLAM_ZFIR0012[Tên_hàng],MATCH(B826,DLAM_ZFIR0012[Mã_hàng_(mã_mới)],0))
```

Danh sách hàng thương mại:
```
=DLAM_MA_HANG_THUONG_MAI[[MA HANG THUONG MAI]:[TEN HANG THUONG MAI]]
```

Doanh thu khác = Tổng ZFIR0012 − các nhóm đã tách:
```
=SUMPRODUCT(
DLAM_ZFIR0012[Thành_tiền(chưa_VAT)],
--(DLAM_ZFIR0012[Year]=H$4),
--(DLAM_ZFIR0012[Month]=H$5)
)/10^6-H824-H835-H836-H837-H838
```

Tổng doanh thu:
```
=H824+H835+H836+H837+H838+H844
```

### 6.7. Danh sách TK chi phí (lọc từ MD_DANH_SACH_TK_SAP)

TK 622, lương trực tiếp:
```
=FILTER(
MD_DANH_SACH_TK_SAP[G/L account SAP],
(--(LEFT(MD_DANH_SACH_TK_SAP[G/L account SAP],3)="622"))
)
```

TK 627 theo Cost group 2, ví dụ lương gián tiếp:
```
=FILTER(
MD_DANH_SACH_TK_SAP[G/L account SAP],
(--(LEFT(MD_DANH_SACH_TK_SAP[G/L account SAP],3)="627"))*
(--(MD_DANH_SACH_TK_SAP[Cost group 2]="Lương gián tiếp sản xuất"))
)
```

TK 627 theo Cost group 2 và 3, ví dụ chi phí hoạt động SX:
```
=FILTER(
MD_DANH_SACH_TK_SAP[G/L account SAP],
(--(LEFT(MD_DANH_SACH_TK_SAP[G/L account SAP],3)="627"))*
(--(MD_DANH_SACH_TK_SAP[Cost group 2]="Sản xuất"))*
(--(MD_DANH_SACH_TK_SAP[Cost group 3]="Cp. Hoạt động"))
)
```

Các mục khác dùng cùng mẫu, chỉ đổi điều kiện:

| Mục | Đầu TK | Cost group 2 | Cost group 3 |
|---|---|---|---|
| 8. Điện SX | 627 | Sản xuất | Điện SX |
| 9. Khấu hao SX | 627 | Khấu hao sản xuất | |
| 10. SXC khác | 627 | "" (trống) | |
| 13. Lương quản lý | 642 | Lương khối quản lý | |
| 14. Chi phí khác quản lý | 642 | Khối quản lý | |
| 15. Khấu hao quản lý | 642 | Khấu hao quản lý | |
| 16. Quản lý khác | 642 | "" (trống) | |

Diễn giải và nhóm chi phí theo TK:
```
=INDEX(MD_DANH_SACH_TK_SAP[Long Text],MATCH(B268,MD_DANH_SACH_TK_SAP[G/L account SAP],0))
=INDEX(MD_DANH_SACH_TK_SAP[Cost group],MATCH($B268,MD_DANH_SACH_TK_SAP[G/L account SAP],0))
=INDEX(MD_DANH_SACH_TK_SAP[Cost group 1],MATCH($B268,MD_DANH_SACH_TK_SAP[G/L account SAP],0))
```

### 6.8. Số tiền chi phí theo TK (FAGLL03)

Dùng cho các mục 5–16 và mục 17:
```
=SUMPRODUCT(
DLAM_FAGLL03_FULL_26[Amount_in_local_currency],
--(DLAM_FAGLL03_FULL_26[G_L_Account]=$B268),
--(DLAM_FAGLL03_FULL_26[Month]=H$5),
--(DLAM_FAGLL03_FULL_26[Year]=H$4)
)/10^6
```

### 6.9. Kiểm tra, KQKD và chi hộ

Check TK mới: có trong FAGLL03 nhưng chưa có trong danh mục:
```
=UNIQUE(
FILTER(
DLAM_FAGLL03_FULL_26[G_L_Account],
((--(LEFT(DLAM_FAGLL03_FULL_26[G_L_Account],3)="622") +
--(LEFT(DLAM_FAGLL03_FULL_26[G_L_Account],3)="627") +
--(LEFT(DLAM_FAGLL03_FULL_26[G_L_Account],3)="641") +
--(LEFT(DLAM_FAGLL03_FULL_26[G_L_Account],3)="642"))>0) *
ISNA(
XMATCH(
DLAM_FAGLL03_FULL_26[G_L_Account],
MD_DANH_SACH_TK_SAP[G/L account SAP])
),"---"))
```

Danh sách chỉ tiêu KQKD từ ZFIR0008:
```
=UNIQUE(DLAM_ZFIR0008[CHỈ_TIÊU])
```

Số liệu từng chỉ tiêu KQKD:
```
=SUMPRODUCT(
DLAM_ZFIR0008[NĂM_NAY],
--(DLAM_ZFIR0008[CHỈ_TIÊU]=$C692),
--(DLAM_ZFIR0008[Year]=H$4),
--(DLAM_ZFIR0008[Month]=H$5)
)/10^6
```

Các khoản chi hộ, tràn 12 tháng:
```
=FILTER(
DLAM_CAC_KHOAN_CHI_HO[[2026_M01]:[2026_M12]],
DLAM_CAC_KHOAN_CHI_HO[NOI_DUNG]=C766
)/10^6
```

### 6.10. Tiêu thức phân bổ (COOIS)

Giờ công theo công đoạn, ILE01:
```
=SUMPRODUCT(
DLAM_COOIS_CONFIRM_2026[Activity_to_conf_1_(ILE01)],
--ISNUMBER(MATCH(DLAM_COOIS_CONFIRM_2026[Order],UNIQUE(DLAM_data_sx_PHAN_LOAI_CHAI[Order]),0)),
--(DLAM_COOIS_CONFIRM_2026[Year]=H$4),
--(DLAM_COOIS_CONFIRM_2026[Month]=H$5)
)
```

Kw điện theo công đoạn, ILE05:
```
=SUMPRODUCT(
DLAM_COOIS_CONFIRM_2026[Activity_to_conf_5_(ILE05)],
--ISNUMBER(MATCH(DLAM_COOIS_CONFIRM_2026[Order],UNIQUE(DLAM_data_sx_PHAN_LOAI_CHAI[Order]),0)),
--(DLAM_COOIS_CONFIRM_2026[Year]=H$4),
--(DLAM_COOIS_CONFIRM_2026[Month]=H$5)
)
```

Tổng tiêu thức:
```
=H718+H719+H720+H721
```

Tỷ trọng phân bổ:
```
=IF(H$717=0,0,H718/H$717)
```

Tiêu thức theo SL bán:
```
=H81
```

Tiêu thức theo SL sau SX:
```
=H52
```

### 6.11. Phân bổ và chi phí đơn vị

Phân bổ chi phí cho công đoạn:
```
=H$267*H723
```

Định phí SXC lấy từ các mục và trừ chi hộ:
```
=H294
```
```
=H310
```
```
=-H765
```

Chi phí đơn vị Vnd/kg = Triệu / Tấn × 10^3:
```
=IF(H$8=0,0,H281/H$8)*10^3
```

NVL đơn vị sau khi trừ hao hụt:
```
=IF(H$8=0,0,(H623-H634)/H$8)*10^3
```

CP bán hàng đơn vị theo SL xuất bán:
```
=IF(H$81=0,0,H450/H$81)*10^3
```

Gộp phân loại chai và nắp:
```
=IF((H$52+H$158)=0,0,(H281+H282)/(H$52+H$158))*10^3
```

Lấy sang bảng tổng hợp chi phí đơn vị:
```
=+H$287
```

### 6.12. Liên kết sang RPT và REPORT

RPT_<TRAM>, link thẳng từ BT:
```
=BT_Dong_Lam!H654
```

REPORT, chi phí gia công đơn vị làm tròn trăm đồng:
```
=ROUND(SUM(BT_Dong_Lam!H$655:H$660),-2)
```

Bình quân 3 trạm:
```
=AVERAGE(E10:E12)
```