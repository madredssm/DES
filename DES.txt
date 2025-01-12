#include <iostream>
#include <fstream>
#include <bitset>
#include <vector>
#include <sstream>
#include <string>
#include <windows.h>
using namespace std;

// Íà÷àëüíàÿ ïåðåñòàíîâêà (òàáë. 1)
int IP[] = { 58, 50, 42, 34, 26, 18, 10, 2,
			60, 52, 44, 36, 28, 20, 12, 4,
			62, 54, 46, 38, 30, 22, 14, 6,
			64, 56, 48, 40, 32, 24, 16, 8,
			57, 49, 41, 33, 25, 17, 9,  1,
			59, 51, 43, 35, 27, 19, 11, 3,
			61, 53, 45, 37, 29, 21, 13, 5,
			63, 55, 47, 39, 31, 23, 15, 7 };

// Îáðàòíàÿ ïåðåñòàíîâêà (òàáë. 8)
int IP_1[] = { 40, 8, 48, 16, 56, 24, 64, 32,
			  39, 7, 47, 15, 55, 23, 63, 31,
			  38, 6, 46, 14, 54, 22, 62, 30,
			  37, 5, 45, 13, 53, 21, 61, 29,
			  36, 4, 44, 12, 52, 20, 60, 28,
			  35, 3, 43, 11, 51, 19, 59, 27,
			  34, 2, 42, 10, 50, 18, 58, 26,
			  33, 1, 41,  9, 49, 17, 57, 25 };

// Íèæå ïðèâåäåíà òàáëèöà, èñïîëüçîâàííàÿ äëÿ ãåíåðàöèè êëþ÷à

// ïåðåñòàíîâêà òàáëèöåé, èç 64-áèòíîãî êëþ÷à â 56-áèòíûé (òàáë. 5)
int PC_1[] = { 57, 49, 41, 33, 25, 17, 9,
			   1, 58, 50, 42, 34, 26, 18,
			  10,  2, 59, 51, 43, 35, 27,
			  19, 11,  3, 60, 52, 44, 36,
			  63, 55, 47, 39, 31, 23, 15,
			   7, 62, 54, 46, 38, 30, 22,
			  14,  6, 61, 53, 45, 37, 29,
			  21, 13,  5, 28, 20, 12,  4 };

// òàáëèöà 7, ñæàòèå ñ 56 áèòîâ äî 48 (òàáë. 7)
int PC_2[] = { 14, 17, 11, 24,  1,  5,
			   3, 28, 15,  6, 21, 10,
			  23, 19, 12,  4, 26,  8,
			  16,  7, 27, 20, 13,  2,
			  41, 52, 31, 37, 47, 55,
			  30, 40, 51, 45, 33, 48,
			  44, 49, 39, 56, 34, 53,
			  46, 42, 50, 36, 29, 32 };

// öèêëè÷åñêèé ñäâèã êàæäîãî êëþ÷à(òàáë. 6)
int shiftBits[] = { 1, 1, 2, 2, 2, 2, 2, 2, 1, 2, 2, 2, 2, 2, 2, 1 };

/**/

// ðàñøèðåíèå äî 48 áèòîâ (òàáë. 2)
int E[] = { 32,  1,  2,  3,  4,  5,
			4,  5,  6,  7,  8,  9,
			8,  9, 10, 11, 12, 13,
		   12, 13, 14, 15, 16, 17,
		   16, 17, 18, 19, 20, 21,
		   20, 21, 22, 23, 24, 25,
		   24, 25, 26, 27, 28, 29,
		   28, 29, 30, 31, 32,  1 };

// òàáëèöà S áëîêîâ (òàáë. 3)
int S_BOX[8][4][16] = {
	{
		{14,4,13,1,2,15,11,8,3,10,6,12,5,9,0,7},
		{0,15,7,4,14,2,13,1,10,6,12,11,9,5,3,8},
		{4,1,14,8,13,6,2,11,15,12,9,7,3,10,5,0},
		{15,12,8,2,4,9,1,7,5,11,3,14,10,0,6,13}
	},
	{
		{15,1,8,14,6,11,3,4,9,7,2,13,12,0,5,10},
		{3,13,4,7,15,2,8,14,12,0,1,10,6,9,11,5},
		{0,14,7,11,10,4,13,1,5,8,12,6,9,3,2,15},
		{13,8,10,1,3,15,4,2,11,6,7,12,0,5,14,9}
	},
	{
		{10,0,9,14,6,3,15,5,1,13,12,7,11,4,2,8},
		{13,7,0,9,3,4,6,10,2,8,5,14,12,11,15,1},
		{13,6,4,9,8,15,3,0,11,1,2,12,5,10,14,7},
		{1,10,13,0,6,9,8,7,4,15,14,3,11,5,2,12}
	},
	{
		{7,13,14,3,0,6,9,10,1,2,8,5,11,12,4,15},
		{13,8,11,5,6,15,0,3,4,7,2,12,1,10,14,9},
		{10,6,9,0,12,11,7,13,15,1,3,14,5,2,8,4},
		{3,15,0,6,10,1,13,8,9,4,5,11,12,7,2,14}
	},
	{
		{2,12,4,1,7,10,11,6,8,5,3,15,13,0,14,9},
		{14,11,2,12,4,7,13,1,5,0,15,10,3,9,8,6},
		{4,2,1,11,10,13,7,8,15,9,12,5,6,3,0,14},
		{11,8,12,7,1,14,2,13,6,15,0,9,10,4,5,3}
	},
	{
		{12,1,10,15,9,2,6,8,0,13,3,4,14,7,5,11},
		{10,15,4,2,7,12,9,5,6,1,13,14,0,11,3,8},
		{9,14,15,5,2,8,12,3,7,0,4,10,1,13,11,6},
		{4,3,2,12,9,5,15,10,11,14,1,7,6,0,8,13}
	},
	{
		{4,11,2,14,15,0,8,13,3,12,9,7,5,10,6,1},
		{13,0,11,7,4,9,1,10,14,3,5,12,2,15,8,6},
		{1,4,11,13,12,3,7,14,10,15,6,8,0,5,9,2},
		{6,11,13,8,1,4,10,7,9,5,0,15,14,2,3,12}
	},
	{
		{13,2,8,4,6,15,11,1,10,9,3,14,5,0,12,7},
		{1,15,13,8,10,3,7,4,12,5,6,11,0,14,9,2},
		{7,11,4,1,9,12,14,2,0,6,10,13,15,3,5,8},
		{2,1,14,7,4,10,8,13,15,12,9,0,3,5,6,11}
	}
};

// Ïåðåñòàíîâêà ïîñëå ñêëåéêè S áëîêîâ 32 áèòà(òàáë. 4)
int P[] = { 16,  7, 20, 21,
		   29, 12, 28, 17,
			1, 15, 23, 26,
			5, 18, 31, 10,
			2,  8, 24, 14,
		   32, 27,  3,  9,
		   19, 13, 30,  6,
		   22, 11,  4, 25 };

/**********************************************************************/
/*                                                                    */
/*                Íèæå ïðèâåäåíà ðåàëèçàöèÿ àëãîðèòìà DES             */
/*                                                                    */
/**********************************************************************/

bitset<64> key;                // 64-áèòíûé êëþ÷
bitset<48> subKey[16];         // Êëþ÷ 48 áèò

/*
 *  Ôóíêöèÿ çàïîëíåíèÿ ïîñëåäíåãî áëîêà
 */
string padString(const string& input) {
	string padded = input;
	size_t paddingSize = 8 - (input.size() % 8); // Ñ÷èòàåì îñòàâøååñÿ ìåñòî
	if (paddingSize < 8) { // Åñëè áëîê íå ïîëíûé, äîáàâëÿåì íóëè
		padded.append(paddingSize, '\0'); // Çàïîëíÿåì íóëÿìè
	}
	return padded;
}

/*
 *  Ôóíêöèÿ óäàëåíèÿ çàïîëíåíèÿ ïîñëå äåøèôðîâêè
 */
string removePadding(const string& input) {
	if (input.empty()) return input;
	char paddingSize = input.back();
	if (paddingSize > 0 && paddingSize <= 8) {
		return input.substr(0, input.size() - paddingSize);
	}
	return input;
}

// Ôóíêöèÿ  f, ïîëó÷àþùàÿ 32-áèòíûé ÷èñëî è 48-áèòíûé êëþ÷, ãåíåðèðóåò 32-ðàçðÿäíûé âûâîä

bitset<32> f(bitset<32> R, bitset<48> k)
{
	bitset<48> expandR;
	// Ðàñøèðåííàÿ çàìåíà, 32->48
	for (int i = 0; i < 48; ++i)
		expandR[47 - i] = R[32 - E[i]];
	//cout << "ñ 32 áèò äî 48 "<<expandR << endl;
	// xor ñ êëþ÷îì 48 áèò
	expandR = expandR ^ k;
	//cout <<endl<< "ÊÑÎÐ Ñ ÊËÞ×ÎÌ" << expandR << endl ;
	// òàáëèöà çàìåíû S_BOX
	bitset<32> output;
	int x = 0;
	for (int i = 0; i < 48; i = i + 6)
	{
		int row = expandR[47 - i] * 2 + expandR[47 - i - 5]; //  expandR[47 - i]: ïåðâûé áèò ãðóïïsû . expandR[47 - i - 5]: ïîñëåäíèé áèò ãðóïïû.
		int col = expandR[47 - i - 1] * 8 + expandR[47 - i - 2] * 4 + expandR[47 - i - 3] * 2 + expandR[47 - i - 4];
		int num = S_BOX[i / 6][row][col];
		bitset<4> binary(num);
		output[31 - x] = binary[3];
		output[31 - x - 1] = binary[2];
		output[31 - x - 2] = binary[1];
		output[31 - x - 3] = binary[0];
		x += 4;
	}
	// P-ïåðåñòàíîâêà, 32->32
	bitset<32> tmp = output;
	for (int i = 0; i < 32; ++i)
		output[31 - i] = tmp[32 - P[i]];
	//cout <<endl << " ïåðåñòàíîâêà 32 - > 32"<< output << endl;
	return output;
}
/*
* öèêëè÷åñêèé ïîáèòîâûé ñäâèã êëþ÷åé âëåâî
*/
bitset<28> leftShift(bitset<28> k, int shift)
{
	bitset<28> tmp = k;
	for (int i = 27; i >= 0; --i)
	{
		if (i - shift < 0)
			k[i] = tmp[i - shift + 28];
		else
			k[i] = tmp[i - shift];
	}
	return k;
}

/*
 *  Ãåíåðàöèÿ 16 48-ðàçðÿäíûõ êëþ÷åé
 */
void generateKeys()
{
	bitset<56> realKey;
	bitset<28> left;
	bitset<28> right;
	bitset<48> compressKey;
	// èç 64 áèò â 56
	for (int i = 0; i < 56; ++i)
		realKey[55 - i] = key[64 - PC_1[i]];
	//cout << " Êëþ÷ 56 áèò" << realKey << endl;
	// ñîõðàíåíèå â 16 ðàóíäîâ
	for (int round = 0; round < 16; ++round)
	{
		for (int i = 28; i < 56; ++i)
			left[i - 28] = realKey[i];
		for (int i = 0; i < 28; ++i)
			right[i] = realKey[i];

		left = leftShift(left, shiftBits[round]);
		right = leftShift(right, shiftBits[round]);
		for (int i = 28; i < 56; ++i)
			realKey[i] = left[i - 28];
		for (int i = 0; i < 28; ++i)
			realKey[i] = right[i];
		//cout << " Êëþ÷ 56 ïîñëå ñêëåéêè áèò" << realKey << endl;
		for (int i = 0; i < 48; ++i)
			compressKey[47 - i] = realKey[56 - PC_2[i]];
		subKey[round] = compressKey;
		//cout << " Êëþ÷ 48 áèò" << realKey << endl;
	}

}

/**
 *  ôóíêöèÿ ïðîåîáðàçîâàíèÿ ìàññèâà ñèìâîëîâ â äâîè÷íûé êîä
 */
bitset<64> charToBitset(const char s[8])
{
	bitset<64> bits;
	for (int i = 0; i < 8; ++i)
		for (int j = 0; j < 8; ++j)
			bits[(7 - i) * 8 + j] = ((s[i] >> j) & 1); // Èçìåíÿåì ïîðÿäîê çàïèñè áàéòîâ
	return bits;
}

/**
 *  øèôðîâàíèå
 */
bitset<64> encrypt(bitset<64>& plain)
{
	cout << endl << "-----------------------------------------------------------------------------------------------------------------------------------------------------" << endl << "Áëîê 64 áèò:" << endl;
	bitset<64> cipher;
	bitset<64> currentBits;
	bitset<32> left;
	bitset<32> right;
	bitset<32> newLeft;
	cout << "\tÈñõîäíûé êëþ÷ â äâîè÷íîì âèäå: \t" << key << endl<< endl;
	cout << "\t\tÎòêðûòûé òåêñò â áèòàõ: " << plain << endl;
	// ïåðâîíà÷àëüíàÿ ïåðåñòàíîâêà
	for (int i = 0; i < 64; ++i)
		currentBits[63 - i] = plain[64 - IP[i]];
	cout << "Ïåðâîíà÷àëüíàÿ ïåðåñòàíîâêà áëîêà (IP): " << currentBits << endl << endl;
	// ïîëó÷åíèå Li è Ri
	for (int i = 32; i < 64; ++i)
		left[i - 32] = currentBits[i];
	for (int i = 0; i < 32; ++i)
		right[i] = currentBits[i];

	// 16 ðàóíäîâ èòåðàöèè 
	for (int round = 0; round < 16; ++round)
	{
		if (round == 0) {
			cout << "\t\t\t\tL0 =\t";
			for (int i = 63; i >= 32; i--) {
				cout << currentBits[i];
			}
			cout << " R0 =\t";
			for (int i = 31; i >= 0; i--) {
				cout << currentBits[i];
			}
			cout << endl;
		}
		if (round == 0)
			cout << "Ðàóíä 1: " << endl;
		if (round == 1)
			cout << "Ðàóíä 2: " << endl;
		if (round == 15)
			cout << "Ðàóíä 16: " << endl;

		if (round == 0)
			cout << "\t\tL" << round << " (+) f(R0, K1)"<< " |\t" << left;
		if (round == 1)
			cout << "\t\tL" << round << " (+) f(R1, K2)" << " |\t" << left;
		if (round == 15)
			cout << "\t\tL" << round << " (+) f(R15, K16)" << " |\t" << left;

		newLeft = right;

		if (round == 0)
			cout << " (+)\t" << f(right, subKey[round]) << endl;
		if (round == 1)
			cout << " (+)\t" << f(right, subKey[round]) << endl;
		if (round == 15)
			cout << " (+)\t" << f(right, subKey[round]) << endl;

		right = left ^ f(right, subKey[round]);

		if (round == 0) {
			cout << "\t\t= R" << round + 1 << "\t\t\t" << right << endl;
		}
		if (round == 1) {
			cout << "\t\t= R" << round + 1 << "\t\t\t" << right << endl;
		}
		if (round == 15) {
			cout << "\t\t= R" << round + 1 << "\t\t\t" << right << endl;
		}

		left = newLeft;

		if (round == 0) {
			cout << "\t\t\t\tL" << round + 1 << ": \t" << left << " ";
			cout << "R" << round + 1 << ": \t" << right << endl;
			cout << "" << endl;
		}
		if (round == 1) {
			cout << "\t\t\t\tL" << round + 1 << ": \t" << left << " ";
			cout << "R" << round + 1 << ": \t" << right << endl;
			cout << "" << endl;
			cout << "\t\t................................................................................................" << endl;
			cout << "\t\t................................................................................................" << endl;
		}
		if (round == 15) {
			cout << "\t\t\t\tL" << round + 1 << ": \t" << left << " ";
			cout << "R" << round + 1 << ": \t" << right << endl;
			cout << "" << endl;
		}
	}

	// Îáúåäèíåíèå L16 è R16, ñêëåèâàíèå áóäåò R16L16*/
	for (int i = 0; i < 32; ++i)
		cipher[i] = left[i];
	for (int i = 32; i < 64; ++i)
		cipher[i] = right[i - 32];
	cout << "Ñêëåèâàíèå L16 R16, ïîìåíÿííûå ìåñòàìè: " << cipher << endl << endl;
	// êîíå÷íàÿ ïåðåñòàíîâêà
	currentBits = cipher;
	for (int i = 0; i < 64; ++i)
		cipher[63 - i] = currentBits[64 - IP_1[i]];
	cout << "Îáðàòíàÿ êîíå÷íàÿ ïåðåñòàíîâêà (IP-1): \t" << cipher << endl;
	// çàøèôðîâàííûé òåêñò
	return cipher;
}

/**
 *  äåøèôðîâàíèå
 */
bitset<64> decrypt(bitset<64>& cipher)
{
	bitset<64> plain;
	bitset<64> currentBits;
	bitset<32> left;
	bitset<32> right;
	bitset<32> newLeft;
	// ïåðâîíà÷àëüíàÿ ïåðåñòàíîâêà
	for (int i = 0; i < 64; ++i)
		currentBits[63 - i] = cipher[64 - IP[i]];
	// ïîëó÷åíèå Li è Ri
	for (int i = 32; i < 64; ++i)
		left[i - 32] = currentBits[i];
	for (int i = 0; i < 32; ++i)
		right[i] = currentBits[i];
	// 16 ðàóíäîâ èòåðàöèè
	for (int round = 0; round < 16; ++round)
	{
		newLeft = right;
		right = left ^ f(right, subKey[15 - round]);
		left = newLeft;
	}
	// Îáúåäèíèòå L16 è R16, ñêëåèâàíèå áóäåò R16L16
	for (int i = 0; i < 32; ++i)
		plain[i] = left[i];
	for (int i = 32; i < 64; ++i)
		plain[i] = right[i - 32];
	// êîíå÷íàÿ ïåðåñòàíîâêà
	currentBits = plain;
	for (int i = 0; i < 64; ++i)
		plain[63 - i] = currentBits[64 - IP_1[i]];
	// ðàñøèôðîâàííûé òåêñò
	return plain;
}


int main() {
	setlocale(LC_ALL, "ru");
	SetConsoleCP(1251);
	SetConsoleOutputCP(1251);

	string k;
	cout << "Ââåäèòå êëþ÷ (8 ñèìâîëîâ): ";
	cin >> k;

	if (k.length() != 8) {
		cerr << "Îøèáêà: êëþ÷ äîëæåí ñîñòîÿòü èç 8 ñèìâîëîâ." << endl;
		return 1;
	}

	key = charToBitset(k.c_str());
	generateKeys(); // Ãåíåðàöèÿ 16-òè ñóáêëþ÷åé

	int inputChoice;
	cout << endl << "Âûáåðèòå ñïîñîá ââîäà òåêñòà/øèôðà:\n";
	cout << "1. Ââîä òåêñòà â êîíñîëü\n";
	cout << "2. ×òåíèå òåêñòà èç ôàéëà\n";
	cout << "Âàø âûáîð: ";
	cin >> inputChoice;

	if (inputChoice != 1 && inputChoice != 2) {
		cerr << "Îøèáêà: íåâåðíûé âûáîð ñïîñîáà ââîäà." << endl;
		return 1;
	}

	int actionChoice;
	cout << endl << "Âûáåðèòå äåéñòâèå:\n";
	cout << "1. Øèôðîâàòü òåêñò\n";
	cout << "2. Äåøèôðîâàòü òåêñò\n";
	cout << "Âàø âûáîð: ";
	cin >> actionChoice;

	if (actionChoice != 1 && actionChoice != 2) {
		cerr << "Îøèáêà: íåâåðíûé âûáîð äåéñòâèÿ." << endl;
		return 1;
	}

	string inputText;
	if (inputChoice == 1) {
		if (actionChoice == 1) {
			cout << "Ââåäèòå òåêñò äëÿ øèôðîâàíèÿ: ";
		}
		else {
			cout << "Ââåäèòå çàøèôðîâàííûå äàííûå (äåñÿòè÷íûå ÷èñëà ÷åðåç ïðîáåë): ";
		}
		cin.ignore(); // Î÷èñòêà áóôåðà ââîäà
		getline(cin, inputText);
	}
	else if (inputChoice == 2) {
		string fileName = (actionChoice == 1) ? "îòêðûòûé òåêñò.txt" : "øèôð.txt";
		ifstream inFile(fileName, ios::binary);
		if (!inFile.is_open()) {
			cerr << "Îøèáêà: Íå óäàëîñü îòêðûòü ôàéë " << fileName << endl;
			return 1;
		}
		inputText = string((istreambuf_iterator<char>(inFile)), istreambuf_iterator<char>());
		inFile.close();
	}

	if (actionChoice == 1) {
		// Øèôðîâàíèå
		cout << "Øèôðîâàíèå íà÷àòî...";
		inputText = padString(inputText); // Äîáàâëåíèå çàïîëíåíèÿ
		string encryptedText;

		vector<bitset<8>> bitRepresentation; // Ñïèñîê 8-áèòíûõ áëîêîâ
		vector<int> decimalRepresentation;   // Ñïèñîê äåñÿòè÷íûõ ÷èñåë

		for (size_t i = 0; i < inputText.size(); i += 8) {
			char block[8];
			memcpy(block, inputText.data() + i, 8);
			bitset<64> plain = charToBitset(block);
			bitset<64> cipher = encrypt(plain);

			// Èçâëåêàåì áèòû â ïðÿìîì ïîðÿäêå
			for (int j = 7; j >= 0; --j) {
				unsigned char byte = static_cast<unsigned char>((cipher.to_ullong() >> (j * 8)) & 0xFF);
				bitRepresentation.push_back(bitset<8>(byte));
				decimalRepresentation.push_back(byte);
				encryptedText += static_cast<char>(byte);
			}
		}

		if (inputChoice == 1) {
			// Âûâîä ðåçóëüòàòîâ â êîíñîëü
			cout << endl<< endl <<"\nÇàøèôðîâàííûé òåêñò:\n";
			cout << "1. Â áèòîâîì ïðåäñòàâëåíèè (8-áèòíûå áëîêè):\n";
			for (const auto& bits : bitRepresentation) {
				cout << bits << " ";
			}
			cout << "\n\n2. Â äåñÿòè÷íîé ñèñòåìå:\n";
			for (const auto& num : decimalRepresentation) {
				cout << num << " ";
			}
			cout << "\n\n3. Â âèäå ñèìâîëîâ:\n";
			cout << encryptedText << endl;
		}
		else {
			// Çàïèñü â ôàéë
			ofstream outFile("øèôð.txt", ios::binary);
			if (!outFile.is_open()) {
				cerr << "Îøèáêà: Íå óäàëîñü îòêðûòü ôàéë øèôð.txt" << endl;
				return 1;
			}
			outFile.write(encryptedText.c_str(), encryptedText.size());
			outFile.close();
			cout << "Øèôðîâàíèå çàâåðøåíî. Ôàéë ñ øèôðîì ïîÿâèëñÿ â ïàïêå." << endl;
		}
	}
	else if (actionChoice == 2) {
		// Äåøèôðîâàíèå
		cout << "Äåøèôðîâàíèå íà÷àòî..." << endl;
		string decryptedText;

		vector<bitset<8>> bitRepresentation; // Ñïèñîê 8-áèòíûõ áëîêîâ
		vector<int> decimalRepresentation;   // Ñïèñîê äåñÿòè÷íûõ ÷èñåë

		if (inputChoice == 1) {
			// Ââîä èç êîíñîëè â äåñÿòè÷íîì ôîðìàòå
			istringstream iss(inputText);
			vector<int> decimalValues((istream_iterator<int>(iss)), istream_iterator<int>());

			if (decimalValues.empty()) {
				cerr << "Îøèáêà: Ââîä ïóñò èëè íåêîððåêòåí." << endl;
				return 1;
			}

			for (size_t i = 0; i < decimalValues.size(); i += 8) {
				bitset<64> cipher;
				for (int j = 0; j < 8 && i + j < decimalValues.size(); ++j) {
					unsigned char ch = static_cast<unsigned char>(decimalValues[i + j]);
					for (int k = 0; k < 8; ++k) {
						cipher[(7 - j) * 8 + k] = (ch >> k) & 1; // Ïðÿìîé ïîðÿäîê áàéòîâ
					}
				}
				bitset<64> plain = decrypt(cipher);

				// Èçâëåêàåì áàéòû èç ðàñøèôðîâàííîãî áëîêà
				for (int j = 7; j >= 0; --j) {
					unsigned char byte = static_cast<unsigned char>((plain.to_ullong() >> (j * 8)) & 0xFF);
					bitRepresentation.push_back(bitset<8>(byte));
					decimalRepresentation.push_back(byte);
					decryptedText += static_cast<char>(byte);
				}
			}
		}
		else {
			// ×òåíèå èç ôàéëà
			for (size_t i = 0; i < inputText.size(); i += 8) {
				bitset<64> cipher;
				for (int j = 0; j < 8; ++j) {
					unsigned char byte = static_cast<unsigned char>(inputText[i + j]);
					for (int k = 0; k < 8; ++k) {
						cipher[(7 - j) * 8 + k] = (byte >> k) & 1; // Ïðÿìîé ïîðÿäîê áàéòîâ
					}
				}
				bitset<64> plain = decrypt(cipher);

				// Èçâëåêàåì áàéòû èç ðàñøèôðîâàííîãî áëîêà
				for (int j = 7; j >= 0; --j) {
					unsigned char byte = static_cast<unsigned char>((plain.to_ullong() >> (j * 8)) & 0xFF);
					bitRepresentation.push_back(bitset<8>(byte));
					decimalRepresentation.push_back(byte);
					decryptedText += static_cast<char>(byte);
				}
			}
		}

		decryptedText = removePadding(decryptedText); // Óäàëåíèå çàïîëíåíèÿ

		if (inputChoice == 1) {
			// Âûâîä â êîíñîëü
			cout << "\nÐàñøèôðîâàííûé òåêñò:\n";
			cout << "1. Â áèòîâîì ïðåäñòàâëåíèè (8-áèòíûå áëîêè):\n";
			for (const auto& bits : bitRepresentation) {
				cout << bits << " ";
			}
			cout << "\n\n2. Â äåñÿòè÷íîé ñèñòåìå:\n";
			for (const auto& num : decimalRepresentation) {
				cout << num << " ";
			}
			cout << "\n\n3. Â âèäå ñèìâîëîâ:\n";
			cout << decryptedText << endl;
		}
		else {
			// Çàïèñü â ôàéë
			ofstream outFile("ðàñøèôðîâàííûé òåêñò.txt", ios::binary);
			if (!outFile.is_open()) {
				cerr << "Îøèáêà: Íå óäàëîñü îòêðûòü ôàéë ðàñøèôðîâàííûé òåêñò.txt" << endl;
				return 1;
			}
			outFile.write(decryptedText.c_str(), decryptedText.size());
			outFile.close();
			cout << "Äåøèôðîâàíèå çàâåðøåíî. Ðàñøèôðîâàííûé ôàéë ïîÿâèëñÿ â ïàïêå." << endl;
		}
	}
	return 0;
}
