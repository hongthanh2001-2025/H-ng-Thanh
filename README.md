# Hong-Thanh
tôi thích viet code 
from flask import Flask, render_template, request, redirect, url_for
import json, os

app = Flask(__name__)

FILE = "don_thu.json"

# Đọc dữ liệu từ file
def doc_don():
    if os.path.exists(FILE):
        with open(FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    return []

# Ghi dữ liệu vào file
def ghi_don(ds_don):
    with open(FILE, "w", encoding="utf-8") as f:
        json.dump(ds_don, f, ensure_ascii=False, indent=4)

@app.route("/")
def index():
    ds_don = doc_don()
    return render_template("index.html", ds_don=ds_don)

@app.route("/them", methods=["GET", "POST"])
def them():
    if request.method == "POST":
        ds_don = doc_don()
        don = {
            "ma_don": request.form["ma_don"],
            "nguoi_gui": request.form["nguoi_gui"],
            "loai_don": request.form["loai_don"],
            "noi_dung": request.form["noi_dung"],
            "trang_thai": "Đã tiếp nhận"
        }
        ds_don.append(don)
        ghi_don(ds_don)
        return redirect(url_for("index"))
    return render_template("them.html")

@app.route("/capnhat/<ma_don>", methods=["GET", "POST"])
def capnhat(ma_don):
    ds_don = doc_don()
    for don in ds_don:
        if don["ma_don"] == ma_don:
            if request.method == "POST":
                don["trang_thai"] = request.form["trang_thai"]
                ghi_don(ds_don)
                return redirect(url_for("index"))
            return render_template("capnhat.html", don=don)
    return "Không tìm thấy đơn"

if __name__ == "__main__":
    app.run(debug=True)
🔹 3. Giao diện HTML (thư mục templates/)
templates/index.html
html
Sao chép mã
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Quản lý đơn</title>
</head>
<body>
    <h2>📋 Danh sách đơn</h2>
    <a href="/them">➕ Thêm đơn mới</a>
    <table border="1" cellpadding="5">
        <tr>
            <th>Mã đơn</th>
            <th>Người gửi</th>
            <th>Loại đơn</th>
            <th>Nội dung</th>
            <th>Trạng thái</th>
            <th>Hành động</th>
        </tr>
        {% for don in ds_don %}
        <tr>
            <td>{{ don.ma_don }}</td>
            <td>{{ don.nguoi_gui }}</td>
            <td>{{ don.loai_don }}</td>
            <td>{{ don.noi_dung }}</td>
            <td>{{ don.trang_thai }}</td>
            <td><a href="/capnhat/{{ don.ma_don }}">Cập nhật</a></td>
        </tr>
        {% endfor %}
    </table>
</body>
</html>
templates/them.html
html
Sao chép mã
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Thêm đơn</title>
</head>
<body>
    <h2>➕ Thêm đơn khiếu nại / tố cáo</h2>
    <form method="post">
        Mã đơn: <input type="text" name="ma_don"><br>
        Người gửi: <input type="text" name="nguoi_gui"><br>
        Loại đơn: 
        <select name="loai_don">
            <option>Khiếu nại</option>
            <option>Tố cáo</option>
        </select><br>
        Nội dung:<br>
        <textarea name="noi_dung"></textarea><br>
        <button type="submit">Lưu</button>
    </form>
</body>
</html>
templates/capnhat.html
html
Sao chép mã
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <title>Cập nhật đơn</title>
</head>
<body>
    <h2>🔄 Cập nhật đơn {{ don.ma_don }}</h2>
    <form method="post">
        Trạng thái: 
        <select name="trang_thai">
            <option {% if don.trang_thai == "Đã tiếp nhận" %}selected{% endif %}>Đã tiếp nhận</option>
            <option {% if don.trang_thai == "Đang xử lý" %}selected{% endif %}>Đang xử lý</option>
            <option {% if don.trang_thai == "Đã giải quyết" %}selected{% endif %}>Đã giải quyết</option>
        </select><br>
        <button type="submit">Cập nhật</button>
    </form>
</body>
</html>
