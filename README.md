//Listas enlazadas
#include<iostream>
#include<cstring>

using namespace std;

struct encabezado{
		int NRS;
		int PR;
		int UR; //
		int URE;
		int PRN; // nuevo
}e;

struct registro{
		int NR;
		char nom[20];
		char ape[20]; 
		int AR;  //
		int SR;
		int ARE;
		int SRN; //nuevo
}n,a,s,r;

FILE *fd;
int le = sizeof(struct encabezado);
int lr = sizeof(struct registro);

void escribir(){
		char rpta; int sgte,pos; bool band;
		e.NRS=0; e.PR = -1; e.UR = -1; e.PRN = -1; e.URE = -1;
		if((fd=fopen("personas.txt","w+t"))==NULL){
				cout<<"no se pudo crear el archivo"<<endl;
				return;
		}
		
		fwrite(&e,le,1,fd); //deja un espacio reservado para el encabezado		
		do{
				n.NR = ++e.NRS;
				cin.ignore();
				cout<<"Apellido:"<<endl; cin.getline(n.ape,20); n.ARE = 0;
				cout<<"Nombre:"<<endl; cin.getline(n.nom,20); 
				
				if(e.PR == -1){//se guardar  el primer registro f sico
						n.SR = e.PR;
						e.PR = n.NR;
						fwrite(&n,lr,1,fd);
				}
				else{//se guardar  un registro que no es primer reg f sico
					//Lectura lista enlazada
					sgte = e.PR; band=false;
					while(sgte!= -1){
						pos = (sgte-1)*lr + le;
						fseek(fd,pos,0);
						fread(&s,lr,1,fd);
						if(strcmp(n.ape,s.ape)>0){
							band=true;
							a = s;
							sgte = s.SR;
							continue;
						}
						break;
					}
					//fin de lectura de lista enlazada
		
					if(band==false){//caso 1:actualiza encabezado
						n.SR = e.PR;
						e.PR = n.NR;
					}
					if(band==true){//caso 2:actualiza reg anterior
						n.SR = a.SR;
						a.SR = n.NR;
						pos=(a.NR-1)*lr + le;
						fseek(fd,pos,0);
						fwrite(&a,lr,1,fd);
					}
				}
				
			    if(e.UR == -1){//se guardar  el ultimo registro f sico
						n.AR = e.UR;
						e.UR = n.NR;
					fwrite(&n,lr,1,fd);
				}
			    else{//se guardar  un registro que no es primer reg f sico
					//Lectura lista enlazada	
					sgte = e.UR; band=false;
					while(sgte!= -1){
						pos = (sgte-1)*lr + le;
						fseek(fd,pos,0);
						fread(&s,lr,1,fd);
						if(strcmp(n.ape,s.ape)<0){
							band=true;
							a = s;
							sgte = s.AR;
							continue;
						}
						break;
					}
					if(band==false){//caso 1:actualiza encabezado
						n.AR = e.UR;
						e.UR = n.NR;
					}
					if(band==true){//caso 2:actualiza reg anterior
						n.AR = a.AR;
						a.AR = n.NR;
						pos=(a.NR-1)*lr + le;
						fseek(fd,pos,0);
						fwrite(&a,lr,1,fd);
					}
				
					}
	//////////////////////////////////////////////////////////////////////////////////////
	           if(e.PRN== -1){//se guardar  el primer registro f sico
						n.SRN = e.PRN;
						e.PRN = n.NR;
						fwrite(&n,lr,1,fd);
				}
			    else{//se guardar  un registro que no es primer reg f sico
					//Lectura lista enlazada
					sgte = e.PRN; band=false;
					while(sgte!= -1){
						pos = (sgte-1)*lr + le;
						fseek(fd,pos,0);
						fread(&s,lr,1,fd);
						if(strcmp(n.nom,s.nom)>0){
							band=true;
							a = s;
							sgte = s.SRN;
							continue;
						}
						break;
					}
					//fin de lectura de lista enlazada
		
					if(band==false){//caso 1:actualiza encabezado
						n.SRN = e.PRN;
						e.PRN = n.NR;
					}
					if(band==true){//caso 2:actualiza reg anterior
						n.SRN = a.SRN;
						a.SRN = n.NR;
						pos=(a.NR-1)*lr + le;
						fseek(fd,pos,0);
						fwrite(&a,lr,1,fd);
					}
				}
				
				pos=(n.NR-1)*lr + le;
				fseek(fd,pos,0);
				fwrite(&n,lr,1,fd); 
				
				cout<<"desea m s registros?"<<endl;
				cin>>rpta;
		} while(rpta=='s' || rpta=='S');
		
		fseek(fd,0,0);
		fwrite(&e,le,1,fd);
		fclose(fd);
}



void eliminarApe(){
	char rpta; int sgte,pos; bool band,flag=false;
	char ape_eliminado[20];
	if((fd=fopen("personas.txt","r+t"))==NULL){
		cout<<"no se pudo abrir el archivo"<<endl;
		return;
	}
	fread(&e,le,1,fd); //deja un espacio reservado para el encabezado		
	cin.ignore();
	cout<<"Ape:"<<endl; cin.getline(ape_eliminado,20);
	//1.Buscar secuencialmente el registro a eliminar
	while(fread(&r,lr,1,fd)){
		if(strcmp(ape_eliminado,r.ape)==0){
			flag=true;
			//2. actualizar URE/ARE
			r.ARE = e.URE;
			e.URE = r.NR;
			break;
		}
	}
	if(flag==false){
		cout<<"apellido no existe!"<<endl;
		return;
	}
	//3. Actualizar la lista PR/SR
	sgte = e.PR; band=false;
	while(sgte!= -1){
		pos = (sgte-1)*lr + le;
		fseek(fd,pos,0);
		fread(&s,lr,1,fd);
		if(strcmp(r.ape,s.ape)>0){
			band=true;
			a = s;
			sgte = s.SR;
			continue;
		}
		break;
	}
			//fin de lectura de lista enlazada
	if(band==false){//caso 1:actualiza encabezado
		e.PR = r.SR;
	}
	if(band==true){//caso 2:actualiza reg anterior
		a.SR = r.SR;
		pos=(a.NR-1)*lr + le;
		fseek(fd,pos,0);
		fwrite(&a,lr,1,fd);
	}
	pos=(r.NR-1)*lr + le;
	fseek(fd,pos,0);
	fwrite(&r,lr,1,fd);

	fseek(fd,0,0);
	fwrite(&e,le,1,fd);
	fclose(fd);
}

void insertar(){
	char rpta; int sgte,pos; bool band;
	if((fd=fopen("personas.txt","r+t"))==NULL){
		cout<<"no se pudo crear el archivo"<<endl;
		return;
	}
	fread(&e,le,1,fd); 
	do{
		n.NR = ++e.NRS;
		cin.ignore();
		cout<<"Apellido:"<<endl; cin.getline(n.ape,20); 
		cout<<"Nombre:"<<endl; cin.getline(n.nom,20); n.ARE =0;
		if(e.PR == -1){//se guardar  el primer registro f sico
			n.SR = e.PR;
			e.PR = n.NR;
			pos = (n.NR -1)*lr + le;
			fseek(fd,pos,0);
			fwrite(&n,lr,1,fd);
		}
		else{//se guardar  un registro que no es primer reg f sico
			//Lectura lista enlazada
			sgte = e.PR; band=false;
			while(sgte!= -1){
				pos = (sgte-1)*lr + le;
				fseek(fd,pos,0);
				fread(&s,lr,1,fd);
				if(strcmp(n.ape,s.ape)>0){
					band=true;
					a = s;
					sgte = s.SR;
					continue;
				}
				break;
			}
			//fin de lectura de lista enlazada
			if(band==false){//caso 1:actualiza encabezado
				n.SR = e.PR;
				e.PR = n.NR;
			}
			if(band==true){//caso 2:actualiza reg anterior
				n.SR = a.SR;
				a.SR = n.NR;
				pos=(a.NR-1)*lr + le;
				fseek(fd,pos,0);
				fwrite(&a,lr,1,fd);
			}
			pos=(n.NR-1)*lr + le;
			fseek(fd,pos,0);
			fwrite(&n,lr,1,fd);
		}
		cout<<"desea m s registros?"<<endl;
		cin>>rpta;
	} while(rpta=='s' || rpta=='S');
	fseek(fd,0,0);
	fwrite(&e,le,1,fd);
	fclose(fd);
}

void ver_archivo(){
		if((fd=fopen("personas.txt","rt"))==NULL){
				cout<<"no se pudo abrir el archivo"<<endl;
				return;
		}
		fread(&e,le,1,fd);
		cout<<"NRS:"<<e.NRS<<"\t\t\t"<<"PR:"<<e.PR<<"   "<<"UR:"<<e.UR<<" "<<"PRN:"<<e.PRN<<"  "<<"URE"<<"\t"<<e.URE<<endl;
		while(fread(&s,lr,1,fd)){
				cout<<s.NR<<"\t"<<s.ape<<"\t"<<s.nom<<"\t"<<s.SR<<"\t"<<s.AR<<"\t"<<s.SRN<<"\t"<<s.ARE<<endl;
		}
		fclose(fd);
}

void ver_lista(){
	int sgte,pos;
	if((fd=fopen("personas.txt","rt"))==NULL){
		cout<<"no se pudo abrir el archivo"<<endl;
		return;
	}
	fread(&e,le,1,fd);
	cout<<"NRS:"<<e.NRS<<"\t\t\t"<<"PR:"<<e.PR<<" "<<"URE"<<"\t"<<e.URE<<endl;
	//Lectura lista enlazada
	sgte = e.PR; 
	while(sgte!= -1){
		pos = (sgte-1)*lr + le;
		fseek(fd,pos,0);
		fread(&s,lr,1,fd);
		cout<<s.NR<<"\t"<<s.ape<<"\t"<<s.nom<<"\t"<<s.SR<<endl;
		sgte = s.SR;
	}
	//fin de lectura de lista enlazada	
	fclose(fd);
}

void ver_listaZ_A(){
	int sgte,pos;
	if((fd=fopen("personas.txt","rt"))==NULL){
		cout<<"no se pudo abrir el archivo"<<endl;
		return;
	}
	fread(&e,le,1,fd);
	cout<<"NRS:"<<e.NRS<<"\t\t\t"<<"UR:"<<e.UR<<" "<<"URE"<<"\t"<<e.URE<<endl;
	//Lectura lista enlazada
	sgte = e.UR; 
	while(sgte!= -1){
		pos = (sgte-1)*lr + le;
		fseek(fd,pos,0);
		fread(&s,lr,1,fd);
		cout<<s.NR<<"\t"<<s.ape<<"\t"<<s.nom<<"\t"<<s.AR<<endl;
		sgte = s.AR;
	}
	//fin de lectura de lista enlazada	
	fclose(fd);
}


int main (int argc, char *argv[]) {
	int op;
	do{
			cout<<"1.escribir 2.ver_Archivo 3.ver_lista 4.ver_listaZ-A 5.insertar 6.eliminarApellidos 7.salir"<<endl;
			cin>>op;
			switch(op){
					case 1: escribir();break;
					case 2: ver_archivo(); break;
					case 3: ver_lista(); break;
				    case 4: ver_listaZ_A(); break;
					case 5: insertar(); break;
					case 6: eliminarApe(); break;
				
			}
	} while(op!= 7);
	return 0;
}
