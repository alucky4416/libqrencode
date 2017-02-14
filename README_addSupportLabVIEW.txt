libqrencde for LabVIEW

libqrencodeについては以下のURLを参照してください。
http://fukuchi.org/works/qrencode/index.html.ja

LabVIEWから呼び出せるように qrencode.c に以下の関数を追加しました。
また、DLL化するための DLLMain()を追加しました。

//==============================================================================
// Add Function
__declspec(dllexport)
int QRcode_encodeStringToU8Array(unsigned char *instr, int instr_len, unsigned int ErrCLevel, unsigned char qrcode_data[], int *qrcode_width)
{
    int casesensitive = 1;
    int version = 0;
    QRecLevel level = QR_ECLEVEL_L;
    QRencodeMode hint = QR_MODE_8;
	QRcode *code;

	if (qrcode_data == NULL || qrcode_width == NULL) return -1;

	switch (ErrCLevel) {
	case 0:
		level = QR_ECLEVEL_L;
		break;
	case 1:
		level = QR_ECLEVEL_M;
		break;
	case 2:
		level = QR_ECLEVEL_Q;
		break;
	case 3:
		level = QR_ECLEVEL_H;
		break;
	default:
		break;
				
	}
	code = QRcode_encodeData(instr_len, instr, version, level);
	if (code == NULL) return -1;

	*qrcode_width = code->width;
	memcpy(qrcode_data, code->data, (code->width * code->width)); // copy QRCode data

	QRcode_free(code);

	return 0;
}

//==============================================================================
// DLL main entry-point functions

int __stdcall DllMain (HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
    switch (fdwReason) {
        case DLL_PROCESS_ATTACH:
            break;
        case DLL_PROCESS_DETACH:
            break;
    }
    
    return 1;
}

int __stdcall DllEntryPoint (HINSTANCE hinstDLL, DWORD fdwReason, LPVOID lpvReserved)
{
    /* Included for compatibility with Borland */

    return DllMain (hinstDLL, fdwReason, lpvReserved);
}

