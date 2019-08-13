---
title: 文件操作工具类FileHandle
date: 2019-08-13 22:32:57
tags:
- 工具类
categories:
- 工具类
---

```
package com.vshow.control.tool;

import java.io.BufferedReader;

import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.FileReader;

import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.UnsupportedEncodingException;
import java.util.ArrayList;
import java.util.List;

public class FileHandle {

	// 删除文件
	public static boolean deleteFile(String sPath) {
		Boolean flag = false;
		File file = new File(sPath);
		// 路径为文件且不为空则进行删除
		if (file.isFile() && file.exists()) {
			file.delete();
			flag = true;
		}
		return flag;
	}

	// 删除文件夹(子文件)
	public static void delFolder(String folderPath) {

		try {

			delAllFile(folderPath); // 删除完里面所有内容

			String filePath = folderPath;

			filePath = filePath.toString();

			java.io.File myFilePath = new java.io.File(filePath);

			myFilePath.delete(); // 删除空文件夹

		} catch (Exception e) {
			
			e.printStackTrace();
		
		}

	}

	public static boolean delAllFile(String path) {

		boolean flag = false;

		File file = new File(path);

		if (!file.exists()) {

			return flag;

		}

		if (!file.isDirectory()) {

			return flag;

		}

		String[] tempList = file.list();

		File temp = null;

		for (int i = 0; i < tempList.length; i++) {

			if (path.endsWith(File.separator)) {

				temp = new File(path + tempList[i]);

			} else {

				temp = new File(path + File.separator + tempList[i]);

			}

			if (temp.isFile()) {

				temp.delete();

			}

			if (temp.isDirectory()) {

				delAllFile(path + "/" + tempList[i]);// 先删除文件夹里面的文件

				delFolder(path + "/" + tempList[i]);// 再删除空文件夹

				flag = true;

			}

		}

		return flag;

	}

	// 获取文件的大小
	public static long getFileByte(String filePath) {
		
		long filesize = 0;
		File f = new File(filePath);
		filesize = f.length();
		return filesize;

	}

	// 拷贝文件夹下所有文件
	// path 源文件夹      path1目标文件夹
	public static void copy(String path, String path1) throws IOException {
		
		File file = new File(path);
		
		File file1 = new File(path1);

		byte[] b = new byte[(int) file.length()];
		
		if (file.isFile()) {
			try {
				FileInputStream is = new FileInputStream(file);
				FileOutputStream ps = new FileOutputStream(file1);
				is.read(b);
				ps.write(b);
			} catch (Exception e) {
				// e.printStackTrace();
			}
		} else if (file.isDirectory()) {
			if (!file.exists())
				file.mkdir();
			String[] list = file.list();
			for (int i = 0; i < list.length; i++) {
				copy(path + "/" + list[i], path1 + "/" + list[i]);
			}
		}

	}

	public static void copy2(String newFile, String oldFile) {
		try {
			
			InputStream in = new FileInputStream(newFile);
			OutputStream out = new FileOutputStream(oldFile);
			byte[] buff = new byte[1024];
			int len = 0;
			while ((len = in.read(buff)) != -1) {
				out.write(buff, 0, len);
			}
			in.close();
			out.close();
			
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	/** 文件写入数据 **/
	public static void readFileInfo(String file, String content)
			throws IOException {
		
		File f = new File(file);
		
		FileOutputStream oldFileWriter = new FileOutputStream(f);
		
		oldFileWriter.write(content.getBytes("UTF-8"));
        
		oldFileWriter.close();
	
	}

	/**
	 * 读取txt文件内容
	 * */
	public static String readTxtFile(String filePath) {
		
		String theRule = "";
		
		try {
            //String encoding = "utf-8";
			String encoding = "";
			
			File file = new File(filePath);
			
			if (file.isFile() && file.exists()) { // 判断文件是否存在
				
				encoding = codeString(filePath);
                InputStreamReader read = new InputStreamReader(
						new FileInputStream(file), encoding);// 考虑到编码格式
				BufferedReader bufferedReader = new BufferedReader(read);
				String lineTxt = null;
				while ((lineTxt = bufferedReader.readLine()) != null) {
					theRule = theRule + lineTxt + "\r\n";
				}
				read.close();
			} else {
				System.out.println("找不到指定的文件");
			}
		} catch (Exception e) {
			System.out.println("读取文件内容出错");
			e.printStackTrace();
		}
		return theRule;

	}

	public static String readFileByLines(String fileName)
			throws UnsupportedEncodingException {
		File file = new File(fileName);

		String returnStr = "";

		BufferedReader reader = null;
		try {

			reader = new BufferedReader(new InputStreamReader(
					new FileInputStream(file), "utf-8"));
			String tempString = null;

			// 一次读入一行，直到读入null为文件结束
			while ((tempString = reader.readLine()) != null) {

				returnStr = returnStr + tempString;

			}
			reader.close();
			
		} catch (IOException e) {

		} finally {
			
			if (reader != null) {
				
				try {
					reader.close();
				} catch (IOException e1) {
					
				}
			}
		}
		return returnStr;
	}

	/** 文件替换数据 **/
	public static void replaceInfos(String url, String oldInfo, String newInfo) {

		try {
			BufferedReader in_ = new BufferedReader(new FileReader(url));

			String line;
			String lines = "";
			while ((line = in_.readLine()) != null) {

				lines = lines + line;

			}
			in_.close();
			lines = lines.replaceAll(oldInfo, newInfo);
			File f = new File(url);
			FileOutputStream oldFileWriter = new FileOutputStream(f);
			oldFileWriter.write(lines.getBytes("UTF-8"));

			oldFileWriter.close();
		} catch (Exception e) {

		}
	}

	/** 判断文件存在不存在 **/
	public static int fileExists(String filePath) {
		File file = new File(filePath);
		/** 不存在返回0,存在返回1 **/
		if (!file.exists()) {
			return 0;
		} else {
			return 1;
		}
	}

	/** 判断文件夹是否存在不存在创建 **/
	public static void fileExists_create(String filePath) {
		File file = new File(filePath);
		/** 不存在返回0,存在返回1 **/
		if (!file.exists()) {
			try {
				file.mkdirs();
			} catch (Exception e) {

			}
		} else {

		}
	}

	public static void copy3(String oldPath, String newPath) {
		try {
			int bytesum = 0;
			int byteread = 0;
			File oldfile = new File(oldPath);
			if (oldfile.exists()) {
				InputStream inStream = new FileInputStream(oldPath);
				FileOutputStream fs = new FileOutputStream(newPath);
				byte[] buffer = new byte[1444];
				int length;
				while ((byteread = inStream.read(buffer)) != -1) {
					bytesum += byteread;
					//System.out.println(bytesum);
					fs.write(buffer, 0, byteread);
				}
				inStream.close();
			}
		} catch (Exception e) {
			System.out.println("error  ");
			e.printStackTrace();
		}
	}
	
	
	/**
	* 判断文件的编码格式
	* @param fileName :file
	* @return 文件编码格式
	* @throws Exception
	*/
	public static String codeString(String fileName) throws Exception{
	BufferedInputStream bin = new BufferedInputStream(
	new FileInputStream(fileName));
	int p = (bin.read() << 8) + bin.read();
	String code = null;

	switch (p) {
	case 0xefbb:
	code = "UTF-8";
	break;
	case 0xfffe:
	code = "Unicode";
	break;
	case 0xfeff:
	code = "UTF-16BE";
	break;
	default:
	code = "GBK";
	}
	return code;
	}
	
	
	
	 /**
     * 读取某个文件夹下的所有文件
     */
    public static List<String> readfile(String filepath) throws FileNotFoundException, IOException {
    	
    	    List<String> fileZips=new ArrayList<String>();
            try {

                    File file = new File(filepath);
                    
                    if (!file.isDirectory()) {
                            //System.out.println("文件");
                            //System.out.println("path=" + file.getPath());
                            //System.out.println("absolutepath=" + file.getAbsolutePath());
                            //System.out.println("name=" + file.getName());
                            //System.out.println(file.getName());
                            if(file.getName().indexOf(".zip")!=-1){
                            	System.out.println("name=" + file.getName());
                            	fileZips.add(file.getName());
                            }
                            

                    } else if (file.isDirectory()) {
                            //System.out.println("文件夹");
                            String[] filelist = file.list();
                            for (int i = 0; i < filelist.length; i++) {
                                    File readfile = new File(filepath + "\\" + filelist[i]);
                                    if (!readfile.isDirectory()) {
                                            //System.out.println("path=" + readfile.getPath());
                                            //System.out.println("absolutepath="
                                             //               + readfile.getAbsolutePath());
                                            //System.out.println("name=" + readfile.getName());
                                            
                                            if(readfile.getName().indexOf(".zip")!=-1){
                                            	//System.out.println("name=" + readfile.getName());
                                            	fileZips.add(readfile.getName());
                                            }

                                    } else if (readfile.isDirectory()) {
                                            readfile(filepath + "\\" + filelist[i]);
                                    }
                            }

                    }

            } catch (FileNotFoundException e) {
                    System.out.println("readfile()   Exception:" + e.getMessage());
            }
            return fileZips;
    }


}

```

