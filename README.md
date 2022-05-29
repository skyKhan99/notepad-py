# notepad-py
Basic Notepad with Python

With this program; could write, open and save text files.
Used Libraries:
-TkInter

Codes:
***********************************************************************************************************************************************************************
import tkinter as tk
import tkinter.messagebox
from tkinter import ttk
from tkinter import filedialog as fd

root = tk.Tk()
root.title("GKHNote")

# apply the grid layout
root.grid_columnconfigure(0, weight=1)
root.grid_rowconfigure(0, weight=1)

# create the text widget
text = tk.Text(root)
text.grid(row=0, column=0, sticky="ewns")

# create a scrollbar widget and set its command to the text widget
scrollbar_v = ttk.Scrollbar(root, orient='vertical', command=text.yview)
scrollbar_v.grid(row=0, column=1, sticky='ns')

#  communicate back to the scrollbar
text['yscrollcommand'] = scrollbar_v.set


class FileInfo:
    def __init__(self, current_file, current_dir):
        self.current_file = current_file
        self.current_dir = current_dir


fi = FileInfo("New File", "")


def open_file():
    text.delete(0.0, 'end')
    dir = fd.askopenfilename(initialdir='/',
                             filetypes=(('Text File', '*.txt'),))
    with open(f"{dir}", "r") as f:
        text.insert(0.0, f.read())

    fi.current_file = dir.split("/")[len(dir.split("/"))-1]
    fi.current_dir = dir
    print(fi.current_file)
    status_bar.config(text=fi.current_file)


def new_file():
    if tkinter.messagebox.askyesno("Save and open new file", "Do you want to save?"):
        if not fi.current_dir == None and not fi.current_file == 'New File':
            with open(f"{fi.current_dir}", "w") as f:
                f.write(text.get(0.0, 'end'))
        else:
            dir = fd.asksaveasfilename()
            with open(f"{dir}.txt", "w") as f:
                f.write(text.get(0.0, 'end'))
    text.delete(0.0, 'end')
    fi.current_file = 'New File'
    status_bar.config(text=fi.current_file)

def save_file():
    if fi.current_file != 'New File':
        with open(f"{fi.current_dir}", "w") as f:
            f.write(text.get(0.0, 'end'))
    else:
        dir = fd.asksaveasfilename()
        with open(f"{dir}.txt", "w") as f:
            f.write(text.get(0.0, 'end'))
        status_bar.config(text=(dir.split("/")[len(dir.split("/"))-1] + ".txt"))


# create menubar
menubar = tk.Menu(root)
root.config(menu=menubar)

file_menu = tk.Menu(
    menubar,
    tearoff=0
)

# add commands to menu
file_menu.add_command(label='New', command=new_file, background='darkblue', foreground='lightblue')
file_menu.add_command(label='Open', command=open_file, background='darkblue', foreground='lightblue')
file_menu.add_command(label='Save', command=save_file, background='darkblue', foreground='lightblue')
menubar.add_cascade(
    label='File',
    menu=file_menu
)

def font():
    fontTk = tkinter.Tk()
    fontTk.resizable(False, False)
    fontTk.geometry('150x250')
    fontTk.title('Fonts')

    fonts = ('Arial','Barlow', 'Calibri', 'Comic Sans MS', 'Consolas', 'Georgia', 'Tahoma', 'Times New Roman')
    sizes = (8, 10, 12, 14, 18, 24, 36, 48, 56, 64, 72, 80)
    listbox = tk.Listbox(fontTk,
                         selectmode='extended',
                         height=12)
    for i in range(len(fonts)):
        listbox.insert(i, fonts[i])
    listbox.grid(
        column=0,
        row=0,
        sticky='nwes'
    )
    size_label = tk.Label(fontTk, text="Size:")
    size_label.grid(
        column=0,
        row=1,
        sticky='e'
    )
    size_entry = tk.Entry(fontTk)
    size_entry.insert(0, "10")
    size_entry.grid(
        column=1,
        row=1,
    )

    def ok_button():
            try:
                size = int(size_entry.get())
                text.config(font=(fonts[listbox.curselection()[0]], size))
            except:
                pass


    btnOk = tk.Button(fontTk, text='Ok', command=ok_button)
    btnOk.grid()
    fontTk.mainloop()


def theme():
    themeTk = tk.Tk()
    themeTk.resizable(False, False)
    themeTk.geometry('200x100')
    themeTk.title('Theme')

    themes = ('Light', 'Dark', 'Matrix', 'Colorful')
    theme_list_box = tk.Listbox(themeTk,
                                selectmode='extended',
                                height=4)
    for i in range(len(themes)):
        theme_list_box.insert(i, themes[i])
    theme_list_box.pack()

    def apply():
        if theme_list_box.curselection()[0] == 0:
            text.config(background='white', foreground='black')
            root.config(background='lightgrey')
            status_bar.config(background='lightgrey', foreground='black')
        elif theme_list_box.curselection()[0] == 1:
            text.config(background='black', foreground='white')
            root.config(background='grey')
            status_bar.config(background='grey', foreground='white')
        elif theme_list_box.curselection()[0] == 2:
            text.config(background='black', foreground='green')
            root.config(background='grey')
            status_bar.config(background='grey', foreground='green')
        elif theme_list_box.curselection()[0] == 3:
            text.config(background='blue', foreground='pink')
            root.config(background='orange')
            status_bar.config(background='orange', foreground='green')
        themeTk.destroy()
    apply_btn = tk.Button(themeTk, text='Apply Theme', command=apply)
    apply_btn.pack()


menubar.add_command(label='Font', command=font)
menubar.add_command(label='Theme', command=theme)
status_bar = tk.Label(root)
status_bar.grid(row=1, column=0)

def on_closing():
    if text.get(0.0, 'end') != "\n":
        if tkinter.messagebox.askyesno("Save and exit", "Do you want to save?"):
            if fi.current_file != 'New File':
                with open(f"{fi.current_dir}", "w") as f:
                    f.write(text.get(0.0, 'end'))
            else:
                dir = fd.asksaveasfilename()
                with open(f"{dir}.txt", "w") as f:
                    f.write(text.get(0.0, 'end'))
    root.destroy()
root.protocol("WM_DELETE_WINDOW", on_closing)
status_bar.config(text=fi.current_file)
root.mainloop()
***********************************************************************************************************************************************************************
