import javax.swing.*;
import javax.swing.filechooser.FileNameExtensionFilter;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.io.File;
import java.io.IOException;
import javax.imageio.ImageIO;

public class SimpleImageViewer extends JFrame {
    private JLabel imageLabel;
    private JLabel filenameLabel;
    private JFileChooser fileChooser;
    private boolean darkMode = false;

    public SimpleImageViewer() {
        initializeUI();
    }

    private void initializeUI() {
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setTitle("Simple Image Viewer");
        setSize(800, 600);

        imageLabel = new JLabel();
        imageLabel.setHorizontalAlignment(JLabel.CENTER);
        imageLabel.setVerticalAlignment(JLabel.CENTER);

        filenameLabel = new JLabel("No Image Selected");
        filenameLabel.setHorizontalAlignment(JLabel.CENTER);

        fileChooser = new JFileChooser();
        fileChooser.setFileFilter(new FileNameExtensionFilter("Image Files", "jpg", "jpeg", "png"));

        JButton openButton = new JButton("Open Image");
        openButton.addActionListener(new OpenActionListener());

        JMenuBar menuBar = new JMenuBar();
        JMenu fileMenu = new JMenu("File");
        JMenuItem openMenuItem = new JMenuItem("Open");
        openMenuItem.addActionListener(new OpenActionListener());
        JMenuItem exitMenuItem = new JMenuItem("Exit");
        exitMenuItem.addActionListener(e -> System.exit(0));

        fileMenu.add(openMenuItem);
        fileMenu.addSeparator();
        fileMenu.add(exitMenuItem);

        JMenu viewMenu = new JMenu("View");
        JMenuItem darkModeMenuItem = new JMenuItem("Toggle Image Name Mode");
        darkModeMenuItem.addActionListener(e -> toggleDarkMode());
        viewMenu.add(darkModeMenuItem);

        menuBar.add(fileMenu);
        menuBar.add(viewMenu);

        JPanel buttonPanel = new JPanel();
        buttonPanel.add(openButton);

        JPanel infoPanel = new JPanel(new BorderLayout());
        infoPanel.add(filenameLabel, BorderLayout.NORTH);
        infoPanel.add(imageLabel, BorderLayout.CENTER);

        add(infoPanel, BorderLayout.CENTER);
        add(buttonPanel, BorderLayout.SOUTH);
        setJMenuBar(menuBar);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private void toggleDarkMode() {
        darkMode = !darkMode;

        Color backgroundColor = darkMode ? Color.BLACK : Color.WHITE;
        Color foregroundColor = darkMode ? Color.WHITE : Color.BLACK;

        getContentPane().setBackground(backgroundColor);
        imageLabel.setBackground(backgroundColor);
        imageLabel.setForeground(foregroundColor);
        filenameLabel.setBackground(backgroundColor);
        filenameLabel.setForeground(foregroundColor);
    }

    private class OpenActionListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            int returnVal = fileChooser.showOpenDialog(SimpleImageViewer.this);

            if (returnVal == JFileChooser.APPROVE_OPTION) {
                File file = fileChooser.getSelectedFile();
                try {

                    if (isValidImageFile(file)) {
                        Image image = ImageIO.read(file);

                        imageLabel.setIcon(new ImageIcon(image));
                        filenameLabel.setText(file.getName());

                        setSize(image.getWidth(null), image.getHeight(null) + 50);
                    } else {
                        JOptionPane.showMessageDialog(SimpleImageViewer.this,
                                "Unsupported image file format", "Error", JOptionPane.ERROR_MESSAGE);
                    }
                } catch (IOException ex) {
                    ex.printStackTrace();
                    JOptionPane.showMessageDialog(SimpleImageViewer.this,
                            "Error loading the image", "Error", JOptionPane.ERROR_MESSAGE);
                }
            }
        }

        private boolean isValidImageFile(File file) {
            String[] supportedExtensions = {"jpg", "jpeg", "png"};
            for (String extension : supportedExtensions) {
                if (file.getName().toLowerCase().endsWith("." + extension)) {
                    return true;
                }
            }
            return false;
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new SimpleImageViewer());
    }
}
