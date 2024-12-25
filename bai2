#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

// Hàm tính giá trị của biểu thức logic
bool evaluateExpression(char var1, bool val1, char var2, bool val2, char var3, bool val3) {
    // Áp dụng các phép toán cơ bản: AND (∧), OR (∨), NOT (¬)
    bool result = false;
    if (var1 == 'A') {
        if (var2 == 'B' && var3 == 'C') {
            result = (val1 || !val2) && val3;
        }
    }
    return result;
}

void generateTruthTable(int numVariables) {
    int numRows = pow(2, numVariables); // Tổng số tổ hợp giá trị của các biến

    cout << "A B C Result" << endl;

    // Duyệt qua tất cả các tổ hợp giá trị True/False cho các biến
    for (int i = 0; i < numRows; i++) {
        bool A = (i & 4) > 0; // Lấy bit thứ 3 (cho A)
        bool B = (i & 2) > 0; // Lấy bit thứ 2 (cho B)
        bool C = (i & 1) > 0; // Lấy bit thứ 1 (cho C)

        // Tính toán giá trị biểu thức và in ra bảng
        bool result = evaluateExpression('A', A, 'B', B, 'C', C);
        
        // In kết quả của biểu thức cho tổ hợp A, B, C
        cout << (A ? "T" : "F") << " "
             << (B ? "T" : "F") << " "
             << (C ? "T" : "F") << " "
             << (result ? "T" : "F") << endl;
    }
}

int main() {
    int numVariables = 3; // Số lượng biến trong biểu thức
    generateTruthTable(numVariables);
    return 0;
}
