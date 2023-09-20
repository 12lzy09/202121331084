# 202121331084
#include <iostream>
#include <fstream>
#include <string>
#include <algorithm>
#include <vector>
#include <sstream>
#include <cctype>
using namespace std;

//将字符串转换为空格
string make_into(const string& input) {
	string result = input;
	for (char& c : result) {
		if (ispunct(c)) {
			c = ' ';
		}
	}
	return result;
}
//将字符串拆分为单词
vector<string> splitIntowords(const string& str) {
	vector<string> words;  //创建一个空的字符串向量，用于存储单词
	stringstream ss(str);  //创建一个字符串流对象，并将待拆分的字符串传入
	string word;          //定义一个字符串变量，用于存储每个单词
	while (ss >> word)    //从字符串流中读取一个单词，直到流中没有更多的单词
	{
		words.push_back(word);  //将读取的单词添加到字符串向量中
	}
	return words;       //返回存储了所有单词的字符串向量
}

//计算重复率
double calcul(const string& ori, const string& cop) {
	string clean_ori = ori;
	string clean_cop = cop;

	//全部转换为小写
	transform(clean_ori.begin(), clean_ori.end(), clean_ori.begin(), ::tolower);
	transform(clean_cop.begin(), clean_cop.end(), clean_cop.begin(), ::tolower);

	//将字符串分割为单词
	vector<string> ori_words = splitIntowords(clean_ori);
	vector<string> cop_words = splitIntowords(clean_cop);

	int total_words = cop_words.size();  //总单词数初值
	int same_words = 0;    //相同单词数初值
	for (const string& word : cop_words) {    //遍历抄袭文件并将其放入word中
		if (find(ori_words.begin(), ori_words.end(), word) != ori_words.end())   //将word中的内容与原文件中所有数据对比，若是相同则返回在区域内查找到的第一个元素，否则指向和end相同
			same_words++;
	}

	return static_cast<double>(same_words) / total_words;
}

int main() {

	string ori_file, cop_file, ans_file;

	cout << "请分别输入原文件、抄袭文件、答案文件的绝对路径：\n";
	cin >> ori_file;  //输入原文件绝对路径
	cin >> cop_file;
	cin >> ans_file;

	ifstream fin_ori(ori_file), fin_cop(cop_file);   //创建文件输入流对象并打开文件
	ofstream fout_ans(ans_file);           //创建文件输出流对象并打开文件

	//分别判断三个文件是否成功打开
	if (fin_ori.is_open() == false) {
		cout << "打开原文件失败\n";
		return 0;
	}
	if (fin_cop.is_open() == false) {
		cout << "打开抄袭文件失败\n";
		return 0;
	}
	if (fout_ans.is_open() == false) {
		cout << "打开答案文件失败\n";
		return 0;
	}

	//将文件中数据分别读入ori_buffer和cop_buffer中
	string ori_buffer((istreambuf_iterator<char>(fin_ori)), istreambuf_iterator<char>());
	string cop_buffer((istreambuf_iterator<char>(fin_cop)), istreambuf_iterator<char>());
	ori_buffer = make_into(ori_buffer);
	cop_buffer = make_into(cop_buffer);
	//计算重复率，并将其写入答案文件
	double ans = calcul(ori_buffer, cop_buffer);
	fout_ans << ans;


	//关闭文件
	fin_ori.close();
	fin_cop.close();
	fout_ans.close();
	cout << ans;

	cout << "写入成功，进入答案文件中查找文件。";

	return 0;
}
