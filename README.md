Save page as pdf  

\\ on the button  // Generate PDF data from the view
    let pdfData = createPDF(from: view)
    
    // Save PDF data to a file
    if let pdfFilePath = savePDF(data: pdfData, fileName: "Reservation.pdf") {
        // Open the saved PDF file
        openPDF(at: pdfFilePath)
    }   other code 
 extension YourVC: UIDocumentInteractionControllerDelegate {
    func documentInteractionControllerViewControllerForPreview(_ controller: UIDocumentInteractionController) -> UIViewController {
        return self
    }
    func createPDF(from view: UIView) -> Data {
        let pdfRenderer = UIGraphicsPDFRenderer(bounds: view.bounds)
        let data = pdfRenderer.pdfData { context in
            context.beginPage()
            view.layer.render(in: context.cgContext)
        }
        return data
    }    func createPDF(fromScrollView scrollView: UIScrollView) -> Data {
        let originalContentOffset = scrollView.contentOffset
        let originalFrame = scrollView.frame
        
        // Adjust the frame of the scrollView to its content size
        scrollView.contentOffset = .zero
        scrollView.frame = CGRect(origin: .zero, size: scrollView.contentSize)
        
        // Create PDF renderer
        let pdfRenderer = UIGraphicsPDFRenderer(bounds: scrollView.bounds)
        let data = pdfRenderer.pdfData { context in
            context.beginPage()
            scrollView.layer.render(in: context.cgContext)
        }
        
        // Restore the original frame and content offset
        scrollView.frame = originalFrame
        scrollView.contentOffset = originalContentOffset
        
        return data
    }
    func openPDF(at filePath: URL) {
        let documentInteractionController = UIDocumentInteractionController(url: filePath)
        documentInteractionController.delegate = self
        documentInteractionController.presentPreview(animated: true)
    }
    
    func savePDF(data: Data, fileName: String) -> URL? {
        // Get the URL for the documents directory
        let documentsDirectory = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first
        let pdfFilePath = documentsDirectory?.appendingPathComponent(fileName)
        
        do {
            // Write the PDF data to the file
            try data.write(to: pdfFilePath!)
            return pdfFilePath
        } catch {
            print("Could not save PDF file: \(error)")
            return nil
        }
    }
}
