#include <iostream>
#include <string>
#include <vector>
#include <map>
#include <stack>
#include <stdexcept>  // For exception handling
#include <sstream> //For stringstream
#include <cctype> // For isalpha

using namespace std; // Using the std namespace


// Define the token types
enum class TokenType {
    LPAREN,  // (
    RPAREN,  // )
    AND,      // ∧
    OR,      // ∨
    IMPLIES, // →
    NOT,     // ¬
    VARIABLE, // A, B, C, etc.
    END,
    INVALID
};

// Token structure
struct Token {
    TokenType type;
    string value;
};


// Error handling class
class ParseError : public runtime_error {
public:
    ParseError(const string& msg) : runtime_error(msg) {}
};


// Lexer (Tokenizes input string)
class Lexer {
private:
    string input;
    int pos = 0;

public:
    Lexer(const string& input) : input(input) {}

    char currentChar() {
         if (pos >= input.length()) {
             return '\0';
         } else {
             return input[pos];
         }
    }

    void advance() {
      pos++;
    }


    void skipWhitespace() {
       while(currentChar() == ' ') {
            advance();
        }
    }

   // added a getPos function so Parser class can have access to pos safely
   int getPos(){
    return pos;
   }

   void setPos(int newPos){
     pos = newPos;
   }

    Token getNextToken() {
        skipWhitespace();

       if (pos >= input.length()) {
            return {TokenType::END, ""};
       }

        char c = currentChar();

        if(c == '(') {
            advance();
            return {TokenType::LPAREN, "("};
        } else if (c == ')') {
           advance();
            return {TokenType::RPAREN, ")"};
        } else if (c == '^') { //Corrected to use single character
           advance();
            return {TokenType::AND, "^"};
        } else if (c == 'v') { // Corrected to use single character
            advance();
            return {TokenType::OR, "v"};
        } else if (c == '>') { //Corrected to use single character
            advance();
            return {TokenType::IMPLIES, ">"};
        } else if (c == '~') { //Corrected to use single character
            advance();
            return {TokenType::NOT, "~"};
        } else if (isalpha(c)) {
            string variable;
            while(isalnum(currentChar())) {
                variable += currentChar();
                advance();
            }
            return {TokenType::VARIABLE, variable};
        }
        else {
             advance();
            return {TokenType::INVALID, string(1,c)};
        }
    }
};


// Abstract syntax tree Node
struct Node {
    TokenType type;
    string value;
    Node* left;
    Node* right;

    Node(TokenType type, const string& value = "", Node* left = nullptr, Node* right = nullptr)
            : type(type), value(value), left(left), right(right) {}

    virtual ~Node() {
        delete left;
        delete right;
    }
};



// Parser
class Parser {
private:
    Lexer lexer;
    Token currentToken;

    Token peekNextToken() {
        int save_pos = lexer.getPos();
        Token t = lexer.getNextToken();
        lexer.setPos(save_pos);
        return t;
    }

    Token getNextToken(){
        Token t = lexer.getNextToken();
        currentToken = t;
        return t;
    }


    void consume(TokenType type) {
        if (currentToken.type == type) {
           getNextToken();
        } else {
            throw ParseError("Unexpected token: Expected " + to_string(static_cast<int>(type)) +
                                ", got " + to_string(static_cast<int>(currentToken.type)));
        }
    }

    Node* term() { // Handle variable or sub-expression
      Token token = currentToken;
       if(token.type == TokenType::VARIABLE) {
           consume(TokenType::VARIABLE);
           return new Node(TokenType::VARIABLE, token.value);
       } else if(token.type == TokenType::LPAREN) {
            consume(TokenType::LPAREN);
            Node* node = expr();
           consume(TokenType::RPAREN);
            return node;
       } else if(token.type == TokenType::NOT) {
           consume(TokenType::NOT);
           Node* rightNode = term();
           return new Node(TokenType::NOT, "", nullptr, rightNode);
       } else {
            throw ParseError("Unexpected Token:" + to_string(static_cast<int>(token.type)));
        }
    }


    Node* factor() {
        Node* leftNode = term();
        while(currentToken.type == TokenType::AND || currentToken.type == TokenType::OR) {
            TokenType op = currentToken.type;
            consume(op);
            Node* rightNode = term();
           leftNode = new Node(op, "", leftNode, rightNode);
        }
        return leftNode;
    }

    Node* expr() {
      Node* leftNode = factor();
       while(currentToken.type == TokenType::IMPLIES) {
           consume(TokenType::IMPLIES);
           Node* rightNode = factor();
            leftNode = new Node(TokenType::IMPLIES, "", leftNode, rightNode);
       }
        return leftNode;

    }



public:
    Parser(const string& input) : lexer(input) {
         getNextToken();
    }

    Node* parse() {
         Node* root = expr();

        if(currentToken.type != TokenType::END) {
          throw ParseError("Unexpected Token After Expression: " + to_string(static_cast<int>(currentToken.type)));
        }
        return root;
    }
};


// Evaluator
class Evaluator {
private:
     map<string, bool> values;

public:
    Evaluator(const map<string, bool>& values) : values(values) {}

    bool evaluate(Node* node) {
        if (node == nullptr) {
             return false; // Should never happen
        }
        switch(node->type) {
            case TokenType::VARIABLE:
                if(values.count(node->value)) {
                    return values.at(node->value);
                } else {
                    throw runtime_error("Variable not found: " + node->value);
                }
                break;
           case TokenType::NOT:
             return !evaluate(node->right);
           case TokenType::AND:
            return evaluate(node->left) && evaluate(node->right);
           case TokenType::OR:
                return evaluate(node->left) || evaluate(node->right);
           case TokenType::IMPLIES:
                return !evaluate(node->left) || evaluate(node->right);
           default:
             return false;
        }
    }
};



int main() {
    string expression;
    cout << "nhap gia tri dau vao: ";
    getline(cin, expression);

    map<string, bool> variableValues;
    cout << "danh sach gia tri cac bien (A:true B:false C:true): ";
     string inputLine;
    getline(cin, inputLine);
    string varName;
    bool value;
    istringstream iss(inputLine); // Move this line before use.
      char colon;
      while(iss >> varName >> colon) {
         if(iss >> boolalpha >> value) {
             variableValues[varName] = value;
         } else {
          cout << "Invalid input" << endl;
           return 1;
         }
    }




    try {
         Parser parser(expression);
        Node* ast = parser.parse();
        Evaluator evaluator(variableValues);
        bool result = evaluator.evaluate(ast);

        cout << "Expression is valid" << endl;
        cout << "Result: " << boolalpha << result << endl;
        delete ast; //Clean up the AST
    } catch (const ParseError& e) {
        cout << "Expression is invalid: " << e.what() << endl;
    } catch (const runtime_error& e) {
      cout << "Error during evaluation: " << e.what() << endl;
    }

    return 0;
}
