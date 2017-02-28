# swiftCreatQRCode

## iOS自带有生成二维码的api

```Swift
//MARK: -传进去字符串,生成二维码图片
    fileprivate func creatQRCodeImage(_ url: String,iconName:String) -> UIImage{
        
        //创建滤镜
        let filter = CIFilter(name: "CIQRCodeGenerator")
        //还原滤镜的默认属性
        filter?.setDefaults()
        //设置需要生成二维码的数据
        filter?.setValue(url.data(using: String.Encoding.utf8), forKey: "inputMessage")
        //从滤镜中取出生成的图片
        let ciImage = filter?.outputImage
        //这个清晰度好
        let bgImage = createNonInterpolatedUIImageFormCIImage(image: ciImage!, size: 300)
        //创建一个头像
        let icon = UIImage(named: iconName)
        //合成图片(把二维码和头像合并)
        let newImage = creatImage(bgImage: bgImage, iconImage: icon!)
        //返回生成好的二维码
        return newImage
    }
    
    //MARK: - 根据背景图片和头像合成头像二维码
    private func creatImage(bgImage: UIImage, iconImage:UIImage) -> UIImage{
        
        //开启图片上下文
        UIGraphicsBeginImageContext(bgImage.size)
        //绘制背景图片
        bgImage.draw(in: CGRect(origin: CGPoint.zero, size: bgImage.size))
        //绘制头像
        let width: CGFloat = 50
        let height: CGFloat = width
        let x = (bgImage.size.width - width) * 0.5
        let y = (bgImage.size.height - height) * 0.5
        iconImage.draw(in: CGRect(x: x, y: y, width: width, height: height))
        //取出绘制好的图片
        let newImage = UIGraphicsGetImageFromCurrentImageContext()
        //关闭上下文
        UIGraphicsEndImageContext()
        //返回合成好的图片
        return newImage!
    }
    
    //MARK: - 根据CIImage生成指定大小的高清UIImage
    private func createNonInterpolatedUIImageFormCIImage(image: CIImage, size: CGFloat) -> UIImage {
        
        let extent: CGRect = image.extent.integral
        let scale: CGFloat = min(size/extent.width, size/extent.height)
        
        let width = extent.width * scale
        let height = extent.height * scale
        let cs: CGColorSpace = CGColorSpaceCreateDeviceGray()
        let bitmapRef = CGContext(data: nil, width: Int(width), height: Int(height), bitsPerComponent: 8, bytesPerRow: 0, space: cs, bitmapInfo: 0)!
        
        let context = CIContext(options: nil)
        let bitmapImage: CGImage = context.createCGImage(image, from: extent)!
        
        bitmapRef.interpolationQuality = CGInterpolationQuality.none
        bitmapRef.scaleBy(x: scale, y: scale);
        bitmapRef.draw(bitmapImage, in: extent)
        
        let scaledImage: CGImage = bitmapRef.makeImage()!
        return UIImage(cgImage: scaledImage)
    }
```
