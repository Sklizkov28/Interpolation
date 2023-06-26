# Interpolation
interpolation

#include <iostream>
#include <math.h>
#include <iomanip>
#include <fstream>
using namespace std;

double f(double x)//задал функцию
{
	return sin(x) + x * x + 1;
}

int factorial(int n) //факториал
{
	if (n < 0) // если введём отрицательное число
		return 0; // возвращаем ноль

	if (n == 0) // если ввести 0
		return 1; // возвращаем факториал от нуля = 1

	else
		return n * factorial(n - 1);//вычисление факториала
}

double NewtonOne(double a, double b, double x, double** delta, int n) // Первая Интерполяционная формула Ньютона
{
	double F = 0;
	double h = (b - a) / n;//шаг интерполяции
	double q = (x - a) / h;
	double chislitel = 1;
	for (int i = 0; i < n; i++)
	{
		for (int j = 0; j < i; j++)
		{
			chislitel *= (q - j);
		}
		if (i == 0)
		{
			F += chislitel * delta[0][1];
		}
		else
		{
			F += (chislitel * delta[0][i + 1]) / factorial(i);
		}
		chislitel = 1;

	}
	return F;
}

void main()
{
	double a = 0;
	const double PI = 3.141592653589793238462643;//задали число пи
	double b = 1.5;

	setlocale(LC_ALL, "ru");

	ofstream fout;
	fout.open("Interpol.txt");
	if (!fout.is_open())
	{
		cout << "Ошибка открытия файла!" << endl;
	}

	else {

		int n = 0;

		cout << "Введите число разбиений " << endl;
		cin >> n;
		double h = (b - a) / n;

		double** delta = new double* [n + 1];//таблица конечных разностей в виде двумерного динамического массива
		for (int i = 0; i <= n; i++)
		{
			delta[i] = new double[n + 2];
		}
		for (int i = 0; i <= n; i++)// 0 строка - значения переменной x_i
		{
			delta[i][0] = 0 + h * i;
			//cout << delta[i][0] << " ";
		}
		//cout << endl;
		for (int i = 0; i <= n; i++)// 1 строка - значения функции f(x_i)
		{
			delta[i][1] = f(delta[i][0]);
			//cout << delta[i][1] << " ";
		}
		//cout << endl;
		for (int m = 1; m <= n + 1; m++)// 2, 3, 4... и все последующие строки - это таблица конечных разностей
		{
			for (int i = 0; i <= n-m; i++)
			{
				delta[i][m + 1] = delta[i + 1][m] - delta[i][m];
				//cout << delta[i][m + 1] << " ";
			}
			//cout << endl;
		}
		

		double xj = 0;
		cout << "Введите начальную границу для xj " << endl;
		cin >> xj;
		cout << endl;

		cout << "xj\t" << setw(16) << "F(xj)\t\t\t\t" << setw(8) << "f(xj)-F(xj)\t"  << setw(30) << "f(xj) :"  << setw(15) <<  "n = " << n <<  endl;
		for (double j = xj; j <= 0.5; j += 0.01)
		{
			/*if (j >= -0.001 && j <= 0.001) 
			{
				cout << 0 << '\t' << setprecision(8) << setw(16) << NewtonOne(a, b, 0, delta, n) << "\t\t" << setw(16) << fabs(f(j) - NewtonOne(a, b, 0, delta, n)) << "\t\t\t" << j << endl;
			}
			else*/
			{
				cout << j << '\t' << setprecision(8) << setw(16) << NewtonOne(a, b, j, delta, n) << "\t\t" << setw(16) << fabs(f(j) - NewtonOne(a, b, j, delta, n)) << "\t\t\t" << setw(16)<< f(j) <<  endl;
			}	
		}
		cout << endl;

		fout << "xj\t\t" << setw(16) << "F(xj)\t\t\t" << setw(14) << "f(xj)-F(xj)\t\t" << "n = " << n << setw(16) << "f(xj):" << endl;
		for (double j = xj; j <= 0.5; j += 0.01)
		{
			/*if (j >= -0.001 && j <= 0.001)
			{
					fout << 0 << '\t' << setprecision(8) << setw(16) << NewtonOne(a, b, 0, delta, n) << "\t\t" << setw(16) << fabs(f(j) - NewtonOne(a, b, 0, delta, n)) << setw(30) << f(j) << endl;
			}
			else*/
			{
				fout << j << '\t' << setprecision(8) << setw(16) << NewtonOne(a, b, j, delta, n) << '\t' << setprecision(8) << setw(16) << fabs(f(j) -  NewtonOne(a, b, j, delta, n)) << setw(30) << f(j) << endl; //<< fabs(f(j) - NewtonOne(a, b, j, delta, n, fout)) << endl;
			}
			
		}
		fout << endl;
		delete[] delta;
	}
	fout.close();

}
