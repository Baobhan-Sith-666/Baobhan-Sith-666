# CODE preview


## QT

> 部分核心代码 

### main.cpp

```cpp
#include "widget.h"
#include <globalpaths.h>
#include <QProcess>
#include <QDebug>
#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    Globalpaths::initializePaths();
    qDebug() << "Base Path initialized to:" << g_basePath;  // 输出基础路径
    Widget w;
    w.show();

    return a.exec();
}
```

### widget.h

> 超多

```cpp

#include "widget.h"
#include "qevent.h"
#include "ui_widget.h"
#include <QIcon>
#include <QFileInfo>
#include <QtConcurrent/QtConcurrent>
#include <QtConcurrent/QtConcurrentRun>
#include <QFutureWatcher>
#include <chartutils.h>
#include <chartview.h>
#include <QStandardItemModel>
// label
Widget::Widget(QWidget *parent)
    : QWidget(parent)
    , ui(new Ui::Widget)
    , engine(nullptr)
    , qmlWindow(nullptr)
    , bridge(new Bridge(this))
    , curveView(nullptr)
    , linearFitView(nullptr)
    , nonLinearFitView(nullptr)
    , multilinechartview(nullptr)
    , multilinechartview2(nullptr)
    , multilinechartview3(nullptr)
    , multilinechartview4(nullptr)
    , multilinechartview5(nullptr)
    , multilinechartview6(nullptr)
{
    ui->setupUi(this);

    setWindowFlags(windowFlags() & ~Qt::WindowStaysOnTopHint); // 确保窗口不是始终置顶
    setAttribute(Qt::WA_ShowWithoutActivating); // 设置窗口属性，显示时不会自动获得焦点

    FilePathIcon = g_basePath + "/core/image/";
    QString PDFfilePATH = g_basePath + "/core/PDF/PDFimage/";

    QFont appFont = QApplication::font();

    ui->btn_set_next->setText("下一页");ui->btn_set_pre->setText("上一页");



    //监视器的位置
    QVBoxLayout *chartLayout = new QVBoxLayout();
    ui->view->setLayout(chartLayout);
    SystemMonitor *monitor = new SystemMonitor(this);
    monitor->monitor(ui->label_Memory_display, ui->progressBar_Memory,
                     ui->label_CPU_display, ui->progressBar_CPU,
                     ui->label_GPU_display, ui->progressBar_GPU,
                     chartLayout);

    setPprogressBarStyle();

    setWindowTitle("高分子科学与材料工程实验（第二版）—— 化学工业出版社");
    QIcon icon(FilePathIcon + "title.png");
    setWindowIcon(icon);

    // List 设置区域
    ui->listWidget_main->setFont(appFont);
    ui->listWidget_main->setIconSize(QSize(30, 30));

    QListWidgetItem *item1 = ui->listWidget_main->item(0);
    QListWidgetItem *item2 = ui->listWidget_main->item(1);
    QListWidgetItem *item3 = ui->listWidget_main->item(2);
    QListWidgetItem *item4 = ui->listWidget_main->item(3);

    QListWidgetItem *item_set1 = ui->listWidget_set->item(0);
    QListWidgetItem *item_set2 = ui->listWidget_set->item(1);
    QListWidgetItem *item_set3 = ui->listWidget_set->item(2);
    QListWidgetItem *item_set4 = ui->listWidget_set->item(3);

    QListWidgetItem *item_addNum01 = ui->listWidget_addNumTodraw_lock->item(0);
    QListWidgetItem *item_addNum02 = ui->listWidget_addNumTodraw_lock->item(1);
    QListWidgetItem *item_addNum03 = ui->listWidget_addNumTodraw_lock->item(2);
    QListWidgetItem *item_addNum04 = ui->listWidget_addNumTodraw_lock->item(3);
    QListWidgetItem *item_addNum05 = ui->listWidget_addNumTodraw_lock->item(4);
    QListWidgetItem *item_addNum06 = ui->listWidget_addNumTodraw_lock->item(5);

    QListWidgetItem *item_main_in01 = ui->listWidget_main_in->item(0);
    QListWidgetItem *item_main_in02 = ui->listWidget_main_in->item(1);

    item1->setIcon(QIcon(FilePathIcon + "home.png"));
    item1->setText("主控");
    item2->setIcon(QIcon(FilePathIcon + "setICON.png"));
    item2->setText("设置");
    item3->setIcon(QIcon(FilePathIcon + "PDF.png"));
    item3->setText("PDF");
    item4->setIcon(QIcon(FilePathIcon + "draw.png"));
    item4->setText("画图");

    item_set1->setIcon(QIcon(FilePathIcon + "test.png"));
    item_set1->setText("图形测试");
    item_set2->setIcon(QIcon(FilePathIcon + "basic.png"));
    item_set2->setText("基础设置");
    item_set3->setIcon(QIcon(FilePathIcon + "task_manager.png"));
    item_set3->setText("内存监视");
    item_set4->setIcon(QIcon(FilePathIcon + "signal.png"));
    item_set4->setText("信号测试");

    item_addNum01->setIcon(QIcon(FilePathIcon + "#007ACC.bmp"));
    item_addNum01->setText("一 ↔ 蓝");
    item_addNum02->setIcon(QIcon(FilePathIcon + "#FF0000.bmp"));
    item_addNum02->setText("二 ↔ 红");
    item_addNum03->setIcon(QIcon(FilePathIcon + "#00B050.bmp"));
    item_addNum03->setText("三 ↔ 绿");
    item_addNum04->setIcon(QIcon(FilePathIcon + "#FFC000.bmp"));
    item_addNum04->setText("四 ↔ 黄");
    item_addNum05->setIcon(QIcon(FilePathIcon + "#9C6AC4.bmp"));
    item_addNum05->setText("五 ↔ 紫");
    item_addNum06->setIcon(QIcon(FilePathIcon + "#FF7F50.bmp"));
    item_addNum06->setText("六 ↔ 橙");

    item_main_in01->setIcon(QIcon(FilePathIcon + "test2.png"));
    item_main_in01->setText("游戏");
    item_main_in02->setIcon(QIcon(FilePathIcon + "start.png"));
    item_main_in02->setText("工作");

    for (int i = 0; i < ui->listWidget_main->count(); ++i) {
        QListWidgetItem* item = ui->listWidget_main->item(i);
        if (item) {
            item->setFont(appFont);
        }
    }

    for (int i = 0; i < ui->listWidget_set->count(); ++i) {
        QListWidgetItem* item = ui->listWidget_set->item(i);
        if (item) {
            item->setFont(appFont);
        }
    }

    for (int i = 0; i < ui->listWidget_addNumTodraw_lock->count(); ++i) {
        QListWidgetItem* item = ui->listWidget_addNumTodraw_lock->item(i);
        if (item) {
            item->setFont(appFont);
        }
    }

    for (int i = 0; i < ui->listWidget_main_in->count(); ++i) {
        QListWidgetItem* item = ui->listWidget_main_in->item(i);
        if (item) {
            item->setFont(appFont);
        }
    }


    QFont font;
    font.setPointSize(14);
    item1->setFont(font);
    item2->setFont(font);
    item3->setFont(font);
    item4->setFont(font);

    item_set1->setFont(font);
    item_set2->setFont(font);
    item_set3->setFont(font);
    item_set4->setFont(font);

    item_addNum01->setFont(font);
    item_addNum02->setFont(font);
    item_addNum03->setFont(font);
    item_addNum04->setFont(font);
    item_addNum05->setFont(font);
    item_addNum06->setFont(font);

    item_main_in01->setFont(font);
    item_main_in02->setFont(font);

    ui->listWidget_main->setStyleSheet(
        "QListWidget::item {"
        "   height: 50px;"
        "   padding-left: 10px;"
        "   padding-right: 10px;"
        "   margin: 5px;"
        "   border: none;"
        "}"
        "QListWidget::item:selected {"
        "   background-color: grey;"
        "   color: white;"
        "   border: none;"
        "}"
        );

    ui->listWidget_set->setStyleSheet(
        "QListWidget::item {"
        "   height: 50px;"
        "   padding-left: 10px;"
        "   padding-right: 10px;"
        "   margin: 5px;"
        "   border: none;"
        "}"
        "QListWidget::item:selected {"
        "   background-color: grey;"
        "   color: white;"
        "   border: none;"
        "}"
        );

    ui->listWidget_addNumTodraw_lock->setStyleSheet(
        "QListWidget::item {"
        "   height: 50px;"
        "   padding-left: 10px;"
        "   padding-right: 10px;"
        "   margin: 5px;"
        "   border: none;"
        "}"
        "QListWidget::item:selected {"
        "   background-color: grey;"
        "   color: white;"
        "   border: none;"
        "}"
        );

    ui->listWidget_main_in->setStyleSheet(
        "QListWidget::item {"
        "   height: 50px;"
        "   padding-left: 10px;"
        "   padding-right: 10px;"
        "   margin: 5px;"
        "   border: none;"
        "}"
        "QListWidget::item:selected {"
        "   background-color: grey;"
        "   color: white;"
        "   border: none;"
        "}"
        );

    connect(ui->listWidget_main, &QListWidget::currentRowChanged, ui->stackedWidget_main, &QStackedWidget::setCurrentIndex);
    connect(ui->listWidget_set, &QListWidget::currentRowChanged, ui->stackedWidget_set, &QStackedWidget::setCurrentIndex);
    connect(ui->listWidget_addNumTodraw_lock, &QListWidget::currentRowChanged, ui->stackedWidget_listWidget_addNumTodraw_lock, &QStackedWidget::setCurrentIndex);
    connect(ui->listWidget_main_in, &QListWidget::currentRowChanged, ui->stackedWidget_main_in, &QStackedWidget::setCurrentIndex);

    // StackedWidget 设置区域
    ui->stackedWidget_main->setStyleSheet(
        "QStackedWidget {"
        "   border: 2px solid #000000;"
        "   border-radius: 5px;"
        "   padding: 5px;"
        "}"
        );

    QPixmap pixmap_01(FilePathIcon + "LineChart.png");
    QPixmap pixmap_02(FilePathIcon + "LineGraph.png");
    QPixmap pixmap_03(FilePathIcon + "LinearFitting.png");
    QPixmap pixmap_04(FilePathIcon + "nonlinearfitview.png");
    ui->label_FoldLine->setScaledContents(true);ui->label_curve->setScaledContents(true);ui->label_LinearFitting->setScaledContents(true);
    ui->label_NonLinearFit->setScaledContents(true);
    ui->label_FoldLine->setPixmap(pixmap_01);ui->label_curve->setPixmap(pixmap_02);ui->label_LinearFitting->setPixmap(pixmap_03);
    ui->label_NonLinearFit->setPixmap(pixmap_04);

    QPixmap pixmap_addNumLabel_01(FilePathIcon + "#007ACC.bmp");
    QPixmap pixmap_addNumLabel_02(FilePathIcon + "#FF0000.bmp");
    QPixmap pixmap_addNumLabel_03(FilePathIcon + "#00B050.bmp");
    QPixmap pixmap_addNumLabel_04(FilePathIcon + "#FFC000.bmp");
    QPixmap pixmap_addNumLabel_05(FilePathIcon + "#9C6AC4.bmp");
    QPixmap pixmap_addNumLabel_06(FilePathIcon + "#FF7F50.bmp");
    ui->label_addNumTodraw_1->setScaledContents(true);ui->label_addNumTodraw_2->setScaledContents(true);
    ui->label_addNumTodraw_3->setScaledContents(true);ui->label_addNumTodraw_4->setScaledContents(true);
    ui->label_addNumTodraw_5->setScaledContents(true);ui->label_addNumTodraw_6->setScaledContents(true);
    ui->label_addNumTodraw_1->setPixmap(pixmap_addNumLabel_01);ui->label_addNumTodraw_2->setPixmap(pixmap_addNumLabel_02);
    ui->label_addNumTodraw_3->setPixmap(pixmap_addNumLabel_03);ui->label_addNumTodraw_4->setPixmap(pixmap_addNumLabel_04);
    ui->label_addNumTodraw_5->setPixmap(pixmap_addNumLabel_05);ui->label_addNumTodraw_6->setPixmap(pixmap_addNumLabel_06);

    // 画图初始化，直接给一个指针
    nonLinearFitView = new NonLinearFitView(ui->charts_NonLinearFit);
    QVBoxLayout *layout_nonLinearFitView = new QVBoxLayout(ui->charts_NonLinearFit);
    layout_nonLinearFitView->addWidget(nonLinearFitView);
    ui->charts_NonLinearFit->setLayout(layout_nonLinearFitView);

    linearFitView = new LinearFitView(ui->charts_LinearFitting);
    QVBoxLayout *layout_linearFitView = new QVBoxLayout(ui->charts_LinearFitting);
    layout_linearFitView->addWidget(linearFitView);
    ui->charts_LinearFitting->setLayout(layout_linearFitView);

    curveView = new CurveView(ui->charts_curve);
    QVBoxLayout *layout_curveView = new QVBoxLayout(ui->charts_curve);
    layout_curveView->addWidget(curveView);
    ui->charts_curve->setLayout(layout_curveView);

    chartView = new ChartView(ui->charts_FoldLine);
    QVBoxLayout *layout_chartView = new QVBoxLayout(ui->charts_FoldLine);
    layout_chartView->addWidget(chartView);
    ui->charts_FoldLine->setLayout(layout_chartView);

    ui->btn_addNumTodraw_lock->setText("锁定");

    multilinechartview = new MultiLineChartView(ui->charts_addLineNum_1);
    QVBoxLayout *layout_MultiLineChartView = new QVBoxLayout(ui->charts_addLineNum_1);
    layout_MultiLineChartView->addWidget(multilinechartview);
    ui->charts_addLineNum_1->setLayout(layout_MultiLineChartView);

    multilinechartview2 = new MultiLineChartView(ui->charts_addLineNum_2);
    QVBoxLayout *layout_multilinechartview2 = new QVBoxLayout(ui->charts_addLineNum_2);
    layout_multilinechartview2->addWidget(multilinechartview2);
    ui->charts_addLineNum_2->setLayout(layout_multilinechartview2);

    multilinechartview3 = new MultiLineChartView(ui->charts_addLineNum_3);
    QVBoxLayout *layout_multilinechartview3 = new QVBoxLayout(ui->charts_addLineNum_3);
    layout_multilinechartview3->addWidget(multilinechartview3);
    ui->charts_addLineNum_3->setLayout(layout_multilinechartview3);

    multilinechartview4 = new MultiLineChartView(ui->charts_addLineNum_4);
    QVBoxLayout *layout_multilinechartview4 = new QVBoxLayout(ui->charts_addLineNum_4);
    layout_multilinechartview4->addWidget(multilinechartview4);
    ui->charts_addLineNum_4->setLayout(layout_multilinechartview4);

    multilinechartview5 = new MultiLineChartView(ui->charts_addLineNum_5);
    QVBoxLayout *layout_multilinechartview5 = new QVBoxLayout(ui->charts_addLineNum_5);
    layout_multilinechartview5->addWidget(multilinechartview5);
    ui->charts_addLineNum_5->setLayout(layout_multilinechartview5);

    multilinechartview6 = new MultiLineChartView(ui->charts_addLineNum_6);
    QVBoxLayout *layout_multilinechartview6 = new QVBoxLayout(ui->charts_addLineNum_6);
    layout_multilinechartview6->addWidget(multilinechartview6);
    ui->charts_addLineNum_6->setLayout(layout_multilinechartview6);

    // 启动外部exe作为加载动画
    QString exePath = g_basePath + "/core/appTEST02_boxed.exe";
    QProcess *loadingProcess = new QProcess(this);
    loadingProcess->start(exePath);

    // 异步加载图片
    QFutureWatcher<void> *watcher = new QFutureWatcher<void>(this);
    connect(watcher, &QFutureWatcher<void>::finished, this, [this, loadingProcess]() {
        loadingProcess->terminate(); // 关闭加载动画
        loadingProcess->waitForFinished();
        this->show(); // 显示主界面
    });

    QFuture<void> future = QtConcurrent::run([this, PDFfilePATH]() {
        // 加载图片的逻辑
        QDir dir(PDFfilePATH);
        QStringList filters;
        filters << "PSMEE_*.png"; // 过滤图片文件
        QStringList files = dir.entryList(filters, QDir::Files); // 获取所有匹配的文件

        QMap<int, QString> sortedFiles;
        for (int i = 0; i < files.size(); ++i) {
            QString file = files[i];
            int startPos = file.indexOf('_') + 1; // 找到下划线位置
            int endPos = file.lastIndexOf('.'); // 找到点的位置
            QString numberStr = file.mid(startPos, endPos - startPos); // 提取数字部分
            int number = numberStr.toInt(); // 转换为整数
            sortedFiles.insert(number, dir.filePath(file)); // 插入到 QMap 中
        }

        QStringList imagePaths = sortedFiles.values();
        for (int i = 0; i < imagePaths.size(); ++i) {
            QIcon icon(imagePaths[i]);
            QListWidgetItem *item = new QListWidgetItem(icon, QString("%1").arg(i + 1));
            ui->listWidget_PDFview->addItem(item);
        }

        emit imagesLoaded(); // 发出图片加载完成信号
    });

    watcher->setFuture(future);

    // 初始化 CustomGraphicsView
    CustomGraphicsView *customView = ui->graphicsView; // 获取提升后的 CustomGraphicsView
    if (customView) {
        customView->setImagePath(PDFfilePATH); // 设置图片路径
        customView->showImage(0); // 显示第一张图片并更新页数
    }

    // 连接按钮信号
    connect(ui->btn_next, &QPushButton::clicked, customView, &CustomGraphicsView::showNextImage);
    connect(ui->btn_pre, &QPushButton::clicked, customView, &CustomGraphicsView::showPreviousImage);
    // 连接页数更新信号
    connect(customView, &CustomGraphicsView::pageChanged, this, &Widget::updatePageLabel);

    // 第二阶段的pdf项目 ↓
    // 设置 QListWidget_PDFview
    ui->listWidget_PDFview->setIconSize(QSize(150, 200)); // 设置图片大小为 150x200
    ui->listWidget_PDFview->setViewMode(QListWidget::IconMode); // 设置为图标模式
    ui->listWidget_PDFview->setSpacing(10); // 设置项目间距
    ui->listWidget_PDFview->setMovement(QListWidget::Static); // 禁用拖动

    // 可选：调整 QListWidget 的样式表
    ui->listWidget_PDFview->setStyleSheet(
        "QListWidget {"
        "   border: 2px solid #ccc;" // 设置边框
        "   padding: 5px;" // 设置内边距
        "}"
        "QListWidget::item {"
        "   height: 250px;" // 设置项目高度
        "   padding: 10px;" // 设置内边距
        "   background-color: #f0f0f0;" // 设置背景色
        "}"
        "QListWidget::item:selected {"
        "   background-color: #ccc;" // 选中时的背景色
        "}"
        );

    connect(ui->listWidget_PDFview, &QListWidget::itemClicked, this, &Widget::onPDFItemClicked);

    //3D
    ChartUtils::setupLineChart(ui->exp_charts);
    ChartUtils::setupBarChart(ui->exp_charts_2);
    ChartUtils::setupScatterChart(ui->exp_charts_3);
    ChartUtils::setupAreaChart(ui->exp_charts_4);
    ChartUtils::setupStackedBarChart(ui->exp_charts_5);
    ChartUtils::setupScatterWithLinearFitChart(ui->exp_charts_6);

    setup3DChart();
    startRotation();
    updateRotation();

    ui->stackedWidget_image->setCurrentIndex(0);
    ui->stackedWidget_set->setCurrentIndex(0);
    ui->stackedWidget_main->setCurrentIndex(0);

    //qml text
    ui->lineEdit_qmlTEXT->setPlaceholderText("等待传入qml文本...");

    // qml ui
    connect(bridge, &Bridge::dataChanged, this, [this]() {
        updateLineEdit(bridge->data());
    });

    connect(bridge, &Bridge::dataSaved, this, [this](const QVariantList &data) {
        QString dataText;
        for (const QVariant &item : data) {
            dataText += item.toString() + "\n"; // 将数组内容转换为字符串
        }
        ui->textEdit->setText(dataText); // 在 textEdit 中显示数组内容

        // 获取存储的数组数据
        QVector<long double> dataArray = bridge->getDataArray();
        qDebug() << "C++ Array:" << &dataArray;
    });

    // 初始化 Table View 的模型
    QStandardItemModel* model = new QStandardItemModel(this);
    model->setHorizontalHeaderLabels({"行号", "X 数据", "Y 数据"});
    ui->tableView->setModel(model);


    ui->tableView->horizontalHeader()->setSectionResizeMode(QHeaderView::ResizeToContents); // 设置列宽自动调整
    ui->tableView->verticalHeader()->setVisible(false); // 关闭垂直表头

    // 连接 Bridge 的 dataSaved_XY 信号
    connect(bridge, &Bridge::dataSaved_XY, this, [ model](const QVariantList &dataX, const QVariantList &dataY) {
        int maxLength = qMax(dataX.size(), dataY.size());
        model->removeRows(0, model->rowCount()); // 清空现有数据

        for (int i = 0; i < maxLength; ++i) {
            QString xValue = (i < dataX.size()) ? dataX[i].toString() : QString("N/A");
            QString yValue = (i < dataY.size()) ? dataY[i].toString() : QString("N/A");

            // 创建新行
            QList<QStandardItem*> rowItems;
            rowItems.append(new QStandardItem(QString::number(i + 1))); // 行号
            rowItems.append(new QStandardItem(xValue)); // X 数据
            rowItems.append(new QStandardItem(yValue)); // Y 数据
            model->appendRow(rowItems);
        }
    });



    ui->label_in_1->setExecutablePath(g_basePath + "/core/widget.exe");
    ui->label_in_1->setImage(FilePathIcon + "3D_draw.png");

    ui->label_in_2->setExecutablePath(g_basePath + "/core/Snake0.2.exe");
    ui->label_in_2->setImage(FilePathIcon + "snake.png");

    ui->label_in_3->setExecutablePath(g_basePath + "/core/appQml_game_universeWar_boxed.exe");
    ui->label_in_3->setImage(FilePathIcon + "airFight.png");

}

Widget::~Widget()
{
    delete ui;
    if (engine) {
        delete engine;  // 确保释放 QQmlApplicationEngine
    }
}

void Widget::updatePageLabel(int current, int total) {
    ui->label->setText(QString("第 %1 页\n共 %2 页").arg(current).arg(total));
}

void Widget::onPDFItemClicked(QListWidgetItem *item) {
    int row = ui->listWidget_PDFview->row(item); // 获取点击项的行号
    if (ui->graphicsView) {
        ui->graphicsView->showImage(row); // 显示对应索引的图片
    }
}

void Widget::wheelEvent(QWheelEvent *event) {
    if (ui->listWidget_PDFview->underMouse()) { // 判断事件是否来自 listWidget_PDFview
        QPoint numPixels = event->pixelDelta(); // 获取像素级别的滚动量
        QPoint numDegrees = event->angleDelta() / 8; // 获取角度级别的滚动量

        int step = 0;
        if (!numPixels.isNull()) {
            step = numPixels.y(); // 使用像素级别的滚动量
        } else if (!numDegrees.isNull()) {
            step = numDegrees.y() / 15; // 将角度转换为步长
        }

        // 调整滚动速度（进一步减小步长）
        step = step / 4; // 将步长调整为原来的 1/4，滚动速度更慢

        // 手动滚动 QListWidget
        QScrollBar *scrollBar = ui->listWidget_PDFview->verticalScrollBar();
        scrollBar->setValue(scrollBar->value() - step);

        event->accept(); // 接受事件，阻止默认行为
    } else {
        QWidget::wheelEvent(event); // 其他控件使用默认行为
    }
}

void Widget::on_btn_set_pre_clicked()
{
    int currentIndex = ui->stackedWidget_image->currentIndex();
    if (currentIndex > 0)
    {
        ui->stackedWidget_image->setCurrentIndex(currentIndex - 1);
    }
    else
    {
        int pageCount = ui->stackedWidget_image->count();
        if (pageCount > 1)
        {
            ui->stackedWidget_image->setCurrentIndex(pageCount - 1);
        }
    }
}


void Widget::on_btn_set_next_clicked()
{
    int currentIndex = ui->stackedWidget_image->currentIndex();
    int pageCount = ui->stackedWidget_image->count();
    if (currentIndex < pageCount - 1)
    {
        ui->stackedWidget_image->setCurrentIndex(currentIndex + 1);
    }
    else
    {
        ui->stackedWidget_image->setCurrentIndex(0);
    }
}

void Widget::setup3DChart()
{
    // 创建 3D 柱状图
    bars = new Q3DBars();

    // 创建数据代理
    QBarDataArray *dataArray = new QBarDataArray;
    QBarDataRow *dataRow1 = new QBarDataRow;
    QBarDataRow *dataRow2 = new QBarDataRow;

    // 添加数据
    *dataRow1 << 1.0f << 3.0f << 7.5f << 5.0f; // 第一行数据
    *dataRow2 << 5.0f << 2.0f << 1.5f << 9.0f; // 第二行数据
    dataArray->append(dataRow1);
    dataArray->append(dataRow2);

    // 设置数据代理
    QBar3DSeries *series = new QBar3DSeries;
    series->dataProxy()->resetArray(dataArray);
    bars->addSeries(series);

    // 设置坐标轴标签
    bars->rowAxis()->setTitle("Row");
    bars->columnAxis()->setTitle("Column");
    bars->valueAxis()->setTitle("Value");

    // 启用旋转和缩放功能
    bars->setSelectionMode(QAbstract3DGraph::SelectionNone); // 禁用选择模式，启用旋转
    bars->activeTheme()->setType(Q3DTheme::ThemeQt); // 设置主题
    bars->activeTheme()->setLabelBackgroundEnabled(false); // 去掉标签背景
    bars->activeTheme()->setLabelTextColor(Qt::white); // 设置标签颜色
    bars->activeTheme()->setGridLineColor(Qt::gray); // 设置网格颜色
    bars->setShadowQuality(QAbstract3DGraph::ShadowQualityNone); // 禁用阴影
    bars->scene()->activeCamera()->setCameraPreset(Q3DCamera::CameraPresetFront); // 设置默认视角

    // 将 3D 图表嵌入到传入的 container 中
    QWidget *chartContainer = QWidget::createWindowContainer(bars, ui->chartContainer);
    chartContainer->setGeometry(0, 0, ui->chartContainer->width(), ui->chartContainer->height());
    chartContainer->setParent(ui->chartContainer);


}

void Widget::startRotation()
{
    // 创建定时器
    QTimer *timer = new QTimer(this);
    connect(timer, &QTimer::timeout, this, &Widget::updateRotation);
    timer->start(50); // 每 50 毫秒更新一次
}

void Widget::updateRotation()
{
    // 获取相机对象
    Q3DCamera *camera = bars->scene()->activeCamera();

    // 获取当前旋转角度
    qreal xRotation = camera->xRotation();

    // 更新旋转角度
    xRotation += 1.0; // 每次增加1度

    // 限制旋转角度范围
    xRotation = fmod(xRotation, 360.0); // 限制在0到360度

    // 设置新的旋转角度
    camera->setXRotation(xRotation);
}

void Widget::setPprogressBarStyle()
{
    ui->progressBar_Memory->setStyleSheet("QProgressBar{text-align: center;"
                                     "background-color: #e2e3e4;"
                                     "border: 1px solid #999999;"
                                     "border-radius: 5px;}"
                                     "QProgressBar::chunk{background-color: #995fff;"
                                     "border-radius: 5px;}");
    ui->progressBar_CPU->setStyleSheet("QProgressBar{text-align: center;"
                                          "background-color: #e2e3e4;"
                                          "border: 1px solid #999999;"
                                          "border-radius: 5px;}"
                                          "QProgressBar::chunk{background-color: #995fff;"
                                          "border-radius: 5px;}");
    ui->progressBar_GPU->setStyleSheet("QProgressBar{text-align: center;"
                                          "background-color: #e2e3e4;"
                                          "border: 1px solid #999999;"
                                          "border-radius: 5px;}"
                                          "QProgressBar::chunk{background-color: #995fff;"
                                          "border-radius: 5px;}");


}

//qml text ↓ #################################################################################
void Widget::on_openQmlButton_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}
void Widget::on_openQmlButton_2_clicked()
{
    // 加载新的 QML 文件
    QQmlApplicationEngine *newEngine = new QQmlApplicationEngine(this);

    // 将桥梁类暴露给新的 QML 文件
    newEngine->rootContext()->setContextProperty("bridge", bridge);

    // 加载新的 QML 文件
    newEngine->load(QUrl("qrc:/rec/qml/TEST.qml"));  // 替换为你的新 QML 文件路径

    if (newEngine->rootObjects().isEmpty()) {
        qWarning() << "Failed to load new QML file";
        return;
    }

    QQuickWindow *newQmlWindow = qobject_cast<QQuickWindow *>(newEngine->rootObjects().first());
    if (!newQmlWindow) {
        qWarning() << "Failed to cast new QML root object to QQuickWindow";
        return;
    }

    newQmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
    newQmlWindow->setTitle("New QML Window");       // 设置窗口标题

    // 连接 closing 信号，确保窗口关闭时删除自身
    QObject::connect(newQmlWindow, &QQuickWindow::closing, this, [newEngine, newQmlWindow]() {
        newQmlWindow->deleteLater();
        newEngine->deleteLater();
    });

    newQmlWindow->show();  // 显示新的 QML 窗口
}



void Widget::updateLineEdit(const QString &data)
{
    ui->lineEdit_qmlTEXT->setText(data);  // 更新 lineEdit 的文本
}

//qml text ↑ #################################################################################

void Widget::on_btn_FoldLine_ui_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_FoldLine_view_clicked()
{
    // 获取桥数组中的数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 确保两个数组的长度一致
    int maxLength = qMax(dataArray_X.size(), dataArray_Y.size());

    // 创建一个标准项模型
    QStandardItemModel* model = new QStandardItemModel(maxLength, 2, this); // 2列：Item, X轴, Y轴

    // 设置表头
    model->setHorizontalHeaderItem(0, new QStandardItem("Item"));
    model->setHorizontalHeaderItem(1, new QStandardItem("X轴"));
    model->setHorizontalHeaderItem(2, new QStandardItem("Y轴"));

    // 填充数据
    for (int i = 0; i < maxLength; ++i) {
        // 设置 Item 列（显示1, 2, 3...）
        model->setItem(i, 0, new QStandardItem(QString::number(i + 1)));

        // 设置 X轴 列
        QString xValue = (i < dataArray_X.size()) ? formatNumber(dataArray_X[i]) : QString("N/A");
        model->setItem(i, 1, new QStandardItem(xValue));

        // 设置 Y轴 列
        QString yValue = (i < dataArray_Y.size()) ? formatNumber(dataArray_Y[i]) : QString("N/A");
        model->setItem(i, 2, new QStandardItem(yValue));
    }

    // 将模型设置到 QTableView 中
    ui->tableView_FoldLine->setModel(model);

    // 调整列宽以适应内容
    ui->tableView_FoldLine->horizontalHeader()->setSectionResizeMode(QHeaderView::ResizeToContents);

    // 隐藏垂直表头（行号）
    ui->tableView_FoldLine->verticalHeader()->setVisible(false);
}

QString Widget::formatNumber(long double value)
{
    if (value == static_cast<long long>(value)) { // 检查是否有小数部分
        return QString::number(static_cast<long long>(value)); // 没有小数部分
    } else {
        // 使用 'g' 格式化，保留最多12位有效数字
        QString numberStr = QString::number(value, 'g', 12);
        return numberStr;
    }
}


void Widget::on_btn_FoldLine_inspect_clicked()
{
    // 获取数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 检查数据是否有效
    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty()) {
        // 如果其中一个数组为空，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴或Y轴数据为空！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    if (dataArray_X.size() != dataArray_Y.size()) {
        // 如果两个数组的大小不匹配，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴和Y轴数据大小不匹配！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    // 如果数据有效，显示提示信息
    CustomMessageBox msgBox;
    msgBox.setText("至少 X 和 Y 对得上");
    msgBox.showCustomMessageBox();
}


void Widget::on_btn_FoldLine_ui_2_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/YlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_FoldLine_draw_clicked()
{
    // 获取数据
    QVector<long double> dataArray_X = bridge->getDataArray();
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();

    // 检查数据有效性
    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty() || dataArray_X.size() != dataArray_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 准备数据
    QVector<QPointF> data;
    for (int i = 0; i < dataArray_X.size(); i++) {
        data.append(QPointF(dataArray_X[i], dataArray_Y[i]));
    }

    // 设置数据到 ChartView
    chartView->setData(data);
}



// 测试用例：
void Widget::showCustomMessageBox() {
    QMessageBox messagebox(this); // 创建QMessageBox对象
    messagebox.setWindowIcon(QIcon(":/rec/images/atom.ico")); // 设置窗口图标
    messagebox.setWindowTitle("提示"); // 设置对话框标题
    messagebox.setText("测试用例"); // 设置文本内容
    messagebox.setIconPixmap(QPixmap(":/rec/images/tip.png").scaled(60, 60)); // 设置自定义图标
    messagebox.addButton("Okay", QMessageBox::AcceptRole); // 添加按钮
    messagebox.addButton("Cancel", QMessageBox::RejectRole);

    // 设置样式表
    messagebox.setStyleSheet(R"(
        QMessageBox {
            background-color: #F2F2F2; /* 背景颜色 */
        }

        QMessageBox QLabel#qt_msgbox_label { /* textLabel */
            color: #298DFF;
            background-color: transparent;
            min-width: 240px; /* 最小宽度 */
            min-height: 40px; /* 最小高度 */
        }

        QMessageBox QLabel#qt_msgboxex_icon_label { /* iconLabel */
            width: 40px;
            height: 40px; /* 图标高度 */
        }

        QMessageBox QPushButton { /* 按钮样式 */
            border: 1px solid #298DFF;
            border-radius: 3px;
            background-color: #F2F2F2;
            color: #298DFF;
            font-family: 'Microsoft YaHei';
            font-size: 10pt;
            min-width: 70px;
            min-height: 25px;
        }

        QMessageBox QPushButton:hover {
            background-color: #298DFF;
            color: #F2F2F2;
        }

        QMessageBox QPushButton:pressed {
            background-color: #257FE6;
        }

        QMessageBox QDialogButtonBox#qt_msgbox_buttonbox { /* 按钮布局 */
            button-layout: 0; /* 设置按钮布局 */
        }
    )");

    // 获取并设置文本标签居中
    QLabel* textlabel = messagebox.findChild<QLabel*>("qt_msgbox_label");
    if (textlabel) {
        textlabel->setAlignment(Qt::AlignCenter); // 设置文本居中
    }

    // 显示对话框
    messagebox.exec();
}

void Widget::loadNewQmlFile() // 直接可以使用的测试案例
{
    QQmlApplicationEngine *newEngine = new QQmlApplicationEngine(this);

    // 将桥梁类暴露给新的 QML 文件
    newEngine->rootContext()->setContextProperty("bridge", bridge);

    // 加载新的 QML 文件
    newEngine->load(QUrl("qrc:/rec/TEST.qml"));  // 替换为你的新 QML 文件路径

    if (newEngine->rootObjects().isEmpty()) {
        qWarning() << "Failed to load new QML file";
        return;
    }

    QQuickWindow *newQmlWindow = qobject_cast<QQuickWindow *>(newEngine->rootObjects().first());
    if (!newQmlWindow) {
        qWarning() << "Failed to cast new QML root object to QQuickWindow";
        return;
    }

    newQmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
    newQmlWindow->setTitle("New QML Window");       // 设置窗口标题

    // 连接 closing 信号，确保窗口关闭时删除自身
    QObject::connect(newQmlWindow, &QQuickWindow::closing, this, [newEngine, newQmlWindow]() {
        newQmlWindow->deleteLater();
        newEngine->deleteLater();
    });

    newQmlWindow->show();  // 显示新的 QML 窗口
}

// btn_FoldLine_ui btn_FoldLine_ui_2 btn_FoldLine_view btn_FoldLine_inspect btn_FoldLine_draw

void Widget::on_btn_curve_ui_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_curve_ui_2_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/YlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_curve_view_clicked()
{
    // 获取桥数组中的数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 确保两个数组的长度一致
    int maxLength = qMax(dataArray_X.size(), dataArray_Y.size());

    // 创建一个标准项模型
    QStandardItemModel* model = new QStandardItemModel(maxLength, 2, this); // 2列：Item, X轴, Y轴

    // 设置表头
    model->setHorizontalHeaderItem(0, new QStandardItem("Item"));
    model->setHorizontalHeaderItem(1, new QStandardItem("X轴"));
    model->setHorizontalHeaderItem(2, new QStandardItem("Y轴"));

    // 填充数据
    for (int i = 0; i < maxLength; ++i) {
        // 设置 Item 列（显示1, 2, 3...）
        model->setItem(i, 0, new QStandardItem(QString::number(i + 1)));

        // 设置 X轴 列
        QString xValue = (i < dataArray_X.size()) ? formatNumber(dataArray_X[i]) : QString("N/A");
        model->setItem(i, 1, new QStandardItem(xValue));

        // 设置 Y轴 列
        QString yValue = (i < dataArray_Y.size()) ? formatNumber(dataArray_Y[i]) : QString("N/A");
        model->setItem(i, 2, new QStandardItem(yValue));
    }

    // 将模型设置到 QTableView 中
    ui->tableView_curve->setModel(model);

    // 调整列宽以适应内容
    ui->tableView_curve->horizontalHeader()->setSectionResizeMode(QHeaderView::ResizeToContents);

    // 隐藏垂直表头（行号）
    ui->tableView_curve->verticalHeader()->setVisible(false);
}


void Widget::on_btn_curve_inspect_clicked()
{
    // 获取数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 检查数据是否有效
    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty()) {
        // 如果其中一个数组为空，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴或Y轴数据为空！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    if (dataArray_X.size() != dataArray_Y.size()) {
        // 如果两个数组的大小不匹配，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴和Y轴数据大小不匹配！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    // 如果数据有效，显示提示信息
    CustomMessageBox msgBox;
    msgBox.setText("至少 X 和 Y 对得上");
    msgBox.showCustomMessageBox();
}


void Widget::on_btn_curve_draw_clicked()
{
    // 获取数据
    QVector<long double> dataArray_X = bridge->getDataArray();
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();

    // 检查数据有效性
    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty() || dataArray_X.size() != dataArray_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 准备数据
    QVector<QPointF> data;
    for (int i = 0; i < dataArray_X.size(); i++) {
        data.append(QPointF(dataArray_X[i], dataArray_Y[i]));
    }

    // 设置数据到 CurveView
    curveView->setData(data);
}

// btn_FoldLine_ui btn_FoldLine_ui_2 btn_FoldLine_view btn_FoldLine_inspect btn_FoldLine_draw

void Widget::on_btn_LinearFitting_ui_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_LinearFitting_ui_2_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/YlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_LinearFitting_view_clicked()
{
    // 获取桥数组中的数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 确保两个数组的长度一致
    int maxLength = qMax(dataArray_X.size(), dataArray_Y.size());

    // 创建一个标准项模型
    QStandardItemModel* model = new QStandardItemModel(maxLength, 2, this); // 2列：Item, X轴, Y轴

    // 设置表头
    model->setHorizontalHeaderItem(0, new QStandardItem("Item"));
    model->setHorizontalHeaderItem(1, new QStandardItem("X轴"));
    model->setHorizontalHeaderItem(2, new QStandardItem("Y轴"));

    // 填充数据
    for (int i = 0; i < maxLength; ++i) {
        // 设置 Item 列（显示1, 2, 3...）
        model->setItem(i, 0, new QStandardItem(QString::number(i + 1)));

        // 设置 X轴 列
        QString xValue = (i < dataArray_X.size()) ? formatNumber(dataArray_X[i]) : QString("N/A");
        model->setItem(i, 1, new QStandardItem(xValue));

        // 设置 Y轴 列
        QString yValue = (i < dataArray_Y.size()) ? formatNumber(dataArray_Y[i]) : QString("N/A");
        model->setItem(i, 2, new QStandardItem(yValue));
    }

    // 将模型设置到 QTableView 中
    ui->tableView_LinearFitting->setModel(model);

    // 调整列宽以适应内容
    ui->tableView_LinearFitting->horizontalHeader()->setSectionResizeMode(QHeaderView::ResizeToContents);

    // 隐藏垂直表头（行号）
    ui->tableView_LinearFitting->verticalHeader()->setVisible(false);
}


void Widget::on_btn_LinearFitting_inspect_clicked()
{
    // 获取数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 检查数据是否有效
    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty()) {
        // 如果其中一个数组为空，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴或Y轴数据为空！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    if (dataArray_X.size() != dataArray_Y.size()) {
        // 如果两个数组的大小不匹配，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴和Y轴数据大小不匹配！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    // 如果数据有效，显示提示信息
    CustomMessageBox msgBox;
    msgBox.setText("至少 X 和 Y 对得上");
    msgBox.showCustomMessageBox();
}


void Widget::on_btn_LinearFitting_draw_clicked()
{
    QVector<long double> dataArray_X = bridge->getDataArray();
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();

    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty() || dataArray_X.size() != dataArray_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    QVector<QPointF> data;
    for (int i = 0; i < dataArray_X.size(); i++) {
        data.append(QPointF(dataArray_X[i], dataArray_Y[i]));
    }

    linearFitView->setData(data);
}

// btn_FoldLine_ui btn_FoldLine_ui_2 btn_FoldLine_view btn_FoldLine_inspect btn_FoldLine_draw


void Widget::on_btn_NonLinearFit_ui_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_NonLinearFit_ui_2_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/YlistUI.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_NonLinearFit_view_clicked()
{
    // 获取桥数组中的数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 确保两个数组的长度一致
    int maxLength = qMax(dataArray_X.size(), dataArray_Y.size());

    // 创建一个标准项模型
    QStandardItemModel* model = new QStandardItemModel(maxLength, 2, this); // 2列：Item, X轴, Y轴

    // 设置表头
    model->setHorizontalHeaderItem(0, new QStandardItem("Item"));
    model->setHorizontalHeaderItem(1, new QStandardItem("X轴"));
    model->setHorizontalHeaderItem(2, new QStandardItem("Y轴"));

    // 填充数据
    for (int i = 0; i < maxLength; ++i) {
        // 设置 Item 列（显示1, 2, 3...）
        model->setItem(i, 0, new QStandardItem(QString::number(i + 1)));

        // 设置 X轴 列
        QString xValue = (i < dataArray_X.size()) ? formatNumber(dataArray_X[i]) : QString("N/A");
        model->setItem(i, 1, new QStandardItem(xValue));

        // 设置 Y轴 列
        QString yValue = (i < dataArray_Y.size()) ? formatNumber(dataArray_Y[i]) : QString("N/A");
        model->setItem(i, 2, new QStandardItem(yValue));
    }

    // 将模型设置到 QTableView 中
    ui->tableView_NonLinearFit->setModel(model);

    // 调整列宽以适应内容
    ui->tableView_NonLinearFit->horizontalHeader()->setSectionResizeMode(QHeaderView::ResizeToContents);

    // 隐藏垂直表头（行号）
    ui->tableView_NonLinearFit->verticalHeader()->setVisible(false);
}


void Widget::on_btn_NonLinearFit_inspect_clicked()
{
    // 获取数据
    QVector<long double> dataArray_X = bridge->getDataArray();    // X轴数据
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();  // Y轴数据

    // 检查数据是否有效
    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty()) {
        // 如果其中一个数组为空，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴或Y轴数据为空！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    if (dataArray_X.size() != dataArray_Y.size()) {
        // 如果两个数组的大小不匹配，报错
        CustomMessageBox msgBox;
        msgBox.setText("错误：X轴和Y轴数据大小不匹配！");
        msgBox.showCustomMessageBox();
        return; // 退出函数
    }

    // 如果数据有效，显示提示信息
    CustomMessageBox msgBox;
    msgBox.setText("至少 X 和 Y 对得上");
    msgBox.showCustomMessageBox();
}


void Widget::on_btn_NonLinearFit_draw_clicked() {
    QVector<long double> dataArray_X = bridge->getDataArray();
    QVector<long double> dataArray_Y = bridge->getDataArray_Y();


    if (dataArray_X.isEmpty() || dataArray_Y.isEmpty() || dataArray_X.size() != dataArray_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    QVector<QPointF> dataPoints;
    for (int i = 0; i < dataArray_X.size(); ++i) {
        dataPoints.append(QPointF(dataArray_X[i], dataArray_Y[i]));
    }

    nonLinearFitView->setData(dataPoints);
}

// openQmlButton
void Widget::on_openQmlButton_3_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XY.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {
            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
} // textEdit




void Widget::on_btn_addNumTodraw_lock_clicked()
{
    // 切换锁定状态
    isLocked = !isLocked;

    if (isLocked) {
        // 锁定状态
        ui->listWidget_addNumTodraw_lock->setEnabled(false);
        ui->btn_addNumTodraw_lock->setText("解锁");
    } else {
        // 解锁状态
        ui->listWidget_addNumTodraw_lock->setEnabled(true);
        ui->btn_addNumTodraw_lock->setText("锁定");
    }
}

void Widget::on_btn_addLine_first_1_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XY.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_first01_X = bridge->getDataArray_XY_X();
            data_addLine_first01_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口

}

void Widget::on_btn_addLine_draw_clicked()
{
    // 确保 multilinechartview 已正确初始化
    if (!multilinechartview) {
        qWarning() << "MultiLineChartView is not initialized!";
        return;
    }

    // 检查数据有效性
    if (data_addLine_first01_X.isEmpty() || data_addLine_first01_Y.isEmpty() ||
        data_addLine_first01_X.size() != data_addLine_first01_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 组织数据为 MultiLineChartView 需要的格式
    QVector<QVector<QPointF>> allData;

    // 第一组数据
    QVector<QPointF> firstLineData;
    for (int i = 0; i < data_addLine_first01_X.size(); ++i) {
        firstLineData.append(QPointF(data_addLine_first01_X[i], data_addLine_first01_Y[i]));
    }
    allData.append(firstLineData);

    // 将数据传递给 MultiLineChartView
    multilinechartview->setData(allData);

}




void Widget::on_btn_addLine_second_1_clicked()
{
    if (!engine) {  // 如果 engine 未创建，则创建并加载 QML 文件
        engine = new QQmlApplicationEngine(this);

        // 将桥梁类暴露给 QML 文件
        engine->rootContext()->setContextProperty("bridge", bridge);

        // 加载 QML 文件
        engine->load(QUrl("qrc:/rec/qml/XY.qml"));  // 替换为你的 QML 文件路径

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));  // 设置窗口最小尺寸
        qmlWindow->setTitle("QML Window");           // 设置窗口标题

        // 连接 closing 信号，确保窗口关闭时删除自身
        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_second01_X = bridge->getDataArray_XY_X();
            data_addLine_second01_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();  // 显示 QML 窗口
}


void Widget::on_btn_addLine_second_2_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_second02_X = bridge->getDataArray_XY_X();
            data_addLine_second02_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_draw_2_clicked()
{
    if (!multilinechartview) {
        qWarning() << "MultiLineChartView is not initialized!";
        return;
    }

    // 检查数据有效性
    if (data_addLine_second01_X.isEmpty() || data_addLine_second01_Y.isEmpty() ||
        data_addLine_second02_X.isEmpty() || data_addLine_second02_Y.isEmpty() ||
        data_addLine_second01_X.size() != data_addLine_second01_Y.size() ||
        data_addLine_second02_X.size() != data_addLine_second02_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 组织数据为 MultiLineChartView 需要的格式
    QVector<QVector<QPointF>> allData;

    // 第一组数据
    QVector<QPointF> firstLineData;
    for (int i = 0; i < data_addLine_second01_X.size(); ++i) {
        firstLineData.append(QPointF(data_addLine_second01_X[i], data_addLine_second01_Y[i]));
    }
    allData.append(firstLineData);

    // 第二组数据
    QVector<QPointF> secondLineData;
    for (int i = 0; i < data_addLine_second02_X.size(); ++i) {
        secondLineData.append(QPointF(data_addLine_second02_X[i], data_addLine_second02_Y[i]));
    }
    allData.append(secondLineData);

    // 将数据传递给 MultiLineChartView
    multilinechartview2->setData(allData);
}

void Widget::on_btn_addLine_third_1_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_third01_X = bridge->getDataArray_XY_X();
            data_addLine_third01_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_third_2_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_third02_X = bridge->getDataArray_XY_X();
            data_addLine_third02_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_third_3_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_third03_X = bridge->getDataArray_XY_X();
            data_addLine_third03_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_draw_3_clicked()
{
    if (!multilinechartview3) {
        qWarning() << "MultiLineChartView3 is not initialized!";
        return;
    }

    // 检查数据有效性
    if (data_addLine_third01_X.isEmpty() || data_addLine_third01_Y.isEmpty() ||
        data_addLine_third02_X.isEmpty() || data_addLine_third02_Y.isEmpty() ||
        data_addLine_third03_X.isEmpty() || data_addLine_third03_Y.isEmpty() ||
        data_addLine_third01_X.size() != data_addLine_third01_Y.size() ||
        data_addLine_third02_X.size() != data_addLine_third02_Y.size() ||
        data_addLine_third03_X.size() != data_addLine_third03_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 组织数据为 MultiLineChartView 需要的格式
    QVector<QVector<QPointF>> allData;

    // 第一组数据
    QVector<QPointF> firstLineData;
    for (int i = 0; i < data_addLine_third01_X.size(); ++i) {
        firstLineData.append(QPointF(data_addLine_third01_X[i], data_addLine_third01_Y[i]));
    }
    allData.append(firstLineData);

    // 第二组数据
    QVector<QPointF> secondLineData;
    for (int i = 0; i < data_addLine_third02_X.size(); ++i) {
        secondLineData.append(QPointF(data_addLine_third02_X[i], data_addLine_third02_Y[i]));
    }
    allData.append(secondLineData);

    // 第三组数据
    QVector<QPointF> thirdLineData;
    for (int i = 0; i < data_addLine_third03_X.size(); ++i) {
        thirdLineData.append(QPointF(data_addLine_third03_X[i], data_addLine_third03_Y[i]));
    }
    allData.append(thirdLineData);

    // 将数据传递给 MultiLineChartView
    multilinechartview3->setData(allData);
}


void Widget::on_btn_addLine_fourth_1_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_forth01_X = bridge->getDataArray_XY_X();
            data_addLine_forth01_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fourth_2_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_forth02_X = bridge->getDataArray_XY_X();
            data_addLine_forth02_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fourth_3_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_forth03_X = bridge->getDataArray_XY_X();
            data_addLine_forth03_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fourth_4_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_forth04_X = bridge->getDataArray_XY_X();
            data_addLine_forth04_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_draw_4_clicked()
{
    if (!multilinechartview4) {
        qWarning() << "MultiLineChartView4 is not initialized!";
        return;
    }

    // 检查数据有效性
    if (data_addLine_forth01_X.isEmpty() || data_addLine_forth01_Y.isEmpty() ||
        data_addLine_forth02_X.isEmpty() || data_addLine_forth02_Y.isEmpty() ||
        data_addLine_forth03_X.isEmpty() || data_addLine_forth03_Y.isEmpty() ||
        data_addLine_forth04_X.isEmpty() || data_addLine_forth04_Y.isEmpty() ||
        data_addLine_forth01_X.size() != data_addLine_forth01_Y.size() ||
        data_addLine_forth02_X.size() != data_addLine_forth02_Y.size() ||
        data_addLine_forth03_X.size() != data_addLine_forth03_Y.size() ||
        data_addLine_forth04_X.size() != data_addLine_forth04_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 组织数据为 MultiLineChartView 需要的格式
    QVector<QVector<QPointF>> allData;

    // 第一组数据
    QVector<QPointF> firstLineData;
    for (int i = 0; i < data_addLine_forth01_X.size(); ++i) {
        firstLineData.append(QPointF(data_addLine_forth01_X[i], data_addLine_forth01_Y[i]));
    }
    allData.append(firstLineData);

    // 第二组数据
    QVector<QPointF> secondLineData;
    for (int i = 0; i < data_addLine_forth02_X.size(); ++i) {
        secondLineData.append(QPointF(data_addLine_forth02_X[i], data_addLine_forth02_Y[i]));
    }
    allData.append(secondLineData);

    // 第三组数据
    QVector<QPointF> thirdLineData;
    for (int i = 0; i < data_addLine_forth03_X.size(); ++i) {
        thirdLineData.append(QPointF(data_addLine_forth03_X[i], data_addLine_forth03_Y[i]));
    }
    allData.append(thirdLineData);

    // 第四组数据
    QVector<QPointF> fourthLineData;
    for (int i = 0; i < data_addLine_forth04_X.size(); ++i) {
        fourthLineData.append(QPointF(data_addLine_forth04_X[i], data_addLine_forth04_Y[i]));
    }
    allData.append(fourthLineData);

    // 将数据传递给 MultiLineChartView
    multilinechartview4->setData(allData);
}

void Widget::on_btn_addLine_fifth_1_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_fifth01_X = bridge->getDataArray_XY_X();
            data_addLine_fifth01_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fifth_2_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_fifth02_X = bridge->getDataArray_XY_X();
            data_addLine_fifth02_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fifth_3_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_fifth03_X = bridge->getDataArray_XY_X();
            data_addLine_fifth03_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fifth_4_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_fifth04_X = bridge->getDataArray_XY_X();
            data_addLine_fifth04_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_fifth_5_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_fifth05_X = bridge->getDataArray_XY_X();
            data_addLine_fifth05_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_draw_5_clicked()
{
    if (!multilinechartview5) {
        qWarning() << "MultiLineChartView5 is not initialized!";
        return;
    }

    // 检查数据有效性
    if (data_addLine_fifth01_X.isEmpty() || data_addLine_fifth01_Y.isEmpty() ||
        data_addLine_fifth02_X.isEmpty() || data_addLine_fifth02_Y.isEmpty() ||
        data_addLine_fifth03_X.isEmpty() || data_addLine_fifth03_Y.isEmpty() ||
        data_addLine_fifth04_X.isEmpty() || data_addLine_fifth04_Y.isEmpty() ||
        data_addLine_fifth05_X.isEmpty() || data_addLine_fifth05_Y.isEmpty() ||
        data_addLine_fifth01_X.size() != data_addLine_fifth01_Y.size() ||
        data_addLine_fifth02_X.size() != data_addLine_fifth02_Y.size() ||
        data_addLine_fifth03_X.size() != data_addLine_fifth03_Y.size() ||
        data_addLine_fifth04_X.size() != data_addLine_fifth04_Y.size() ||
        data_addLine_fifth05_X.size() != data_addLine_fifth05_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 组织数据为 MultiLineChartView 需要的格式
    QVector<QVector<QPointF>> allData;

    // 第一组数据
    QVector<QPointF> firstLineData;
    for (int i = 0; i < data_addLine_fifth01_X.size(); ++i) {
        firstLineData.append(QPointF(data_addLine_fifth01_X[i], data_addLine_fifth01_Y[i]));
    }
    allData.append(firstLineData);

    // 第二组数据
    QVector<QPointF> secondLineData;
    for (int i = 0; i < data_addLine_fifth02_X.size(); ++i) {
        secondLineData.append(QPointF(data_addLine_fifth02_X[i], data_addLine_fifth02_Y[i]));
    }
    allData.append(secondLineData);

    // 第三组数据
    QVector<QPointF> thirdLineData;
    for (int i = 0; i < data_addLine_fifth03_X.size(); ++i) {
        thirdLineData.append(QPointF(data_addLine_fifth03_X[i], data_addLine_fifth03_Y[i]));
    }
    allData.append(thirdLineData);

    // 第四组数据
    QVector<QPointF> fourthLineData;
    for (int i = 0; i < data_addLine_fifth04_X.size(); ++i) {
        fourthLineData.append(QPointF(data_addLine_fifth04_X[i], data_addLine_fifth04_Y[i]));
    }
    allData.append(fourthLineData);

    // 第五组数据
    QVector<QPointF> fifthLineData;
    for (int i = 0; i < data_addLine_fifth05_X.size(); ++i) {
        fifthLineData.append(QPointF(data_addLine_fifth05_X[i], data_addLine_fifth05_Y[i]));
    }
    allData.append(fifthLineData);

    // 将数据传递给 MultiLineChartView
    multilinechartview5->setData(allData);
}


void Widget::on_btn_addLine_sixth_1_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_sixth01_X = bridge->getDataArray_XY_X();
            data_addLine_sixth01_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_sixth_2_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_sixth02_X = bridge->getDataArray_XY_X();
            data_addLine_sixth02_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_sixth_3_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_sixth03_X = bridge->getDataArray_XY_X();
            data_addLine_sixth03_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_sixth_4_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_sixth04_X = bridge->getDataArray_XY_X();
            data_addLine_sixth04_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_sixth_5_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_sixth05_X = bridge->getDataArray_XY_X();
            data_addLine_sixth05_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_sixth_6_clicked()
{
    if (!engine) {
        engine = new QQmlApplicationEngine(this);

        engine->rootContext()->setContextProperty("bridge", bridge);

        engine->load(QUrl("qrc:/rec/qml/XY.qml"));

        if (engine->rootObjects().isEmpty()) {
            qWarning() << "Failed to load QML file";
            return;
        }

        qmlWindow = qobject_cast<QQuickWindow *>(engine->rootObjects().first());
        if (!qmlWindow) {
            qWarning() << "Failed to cast QML root object to QQuickWindow";
            return;
        }

        qmlWindow->setMinimumSize(QSize(400, 300));
        qmlWindow->setTitle("QML Window");

        QObject::connect(qmlWindow, &QQuickWindow::closing, this, [this]() {

            data_addLine_sixth06_X = bridge->getDataArray_XY_X();
            data_addLine_sixth06_Y = bridge->getDataArray_XY_Y();

            qmlWindow->deleteLater();
            engine->deleteLater();
            qmlWindow = nullptr;
            engine = nullptr;
        });
    }

    qmlWindow->show();
}


void Widget::on_btn_addLine_draw_6_clicked()
{
    if (!multilinechartview6) {
        qWarning() << "MultiLineChartView6 is not initialized!";
        return;
    }

    // 检查数据有效性
    if (data_addLine_sixth01_X.isEmpty() || data_addLine_sixth01_Y.isEmpty() ||
        data_addLine_sixth02_X.isEmpty() || data_addLine_sixth02_Y.isEmpty() ||
        data_addLine_sixth03_X.isEmpty() || data_addLine_sixth03_Y.isEmpty() ||
        data_addLine_sixth04_X.isEmpty() || data_addLine_sixth04_Y.isEmpty() ||
        data_addLine_sixth05_X.isEmpty() || data_addLine_sixth05_Y.isEmpty() ||
        data_addLine_sixth06_X.isEmpty() || data_addLine_sixth06_Y.isEmpty() ||
        data_addLine_sixth01_X.size() != data_addLine_sixth01_Y.size() ||
        data_addLine_sixth02_X.size() != data_addLine_sixth02_Y.size() ||
        data_addLine_sixth03_X.size() != data_addLine_sixth03_Y.size() ||
        data_addLine_sixth04_X.size() != data_addLine_sixth04_Y.size() ||
        data_addLine_sixth05_X.size() != data_addLine_sixth05_Y.size() ||
        data_addLine_sixth06_X.size() != data_addLine_sixth06_Y.size()) {
        qWarning() << "数据无效或长度不一致";
        return;
    }

    // 组织数据为 MultiLineChartView 需要的格式
    QVector<QVector<QPointF>> allData;

    // 第一组数据
    QVector<QPointF> firstLineData;
    for (int i = 0; i < data_addLine_sixth01_X.size(); ++i) {
        firstLineData.append(QPointF(data_addLine_sixth01_X[i], data_addLine_sixth01_Y[i]));
    }
    allData.append(firstLineData);

    // 第二组数据
    QVector<QPointF> secondLineData;
    for (int i = 0; i < data_addLine_sixth02_X.size(); ++i) {
        secondLineData.append(QPointF(data_addLine_sixth02_X[i], data_addLine_sixth02_Y[i]));
    }
    allData.append(secondLineData);

    // 第三组数据
    QVector<QPointF> thirdLineData;
    for (int i = 0; i < data_addLine_sixth03_X.size(); ++i) {
        thirdLineData.append(QPointF(data_addLine_sixth03_X[i], data_addLine_sixth03_Y[i]));
    }
    allData.append(thirdLineData);

    // 第四组数据
    QVector<QPointF> fourthLineData;
    for (int i = 0; i < data_addLine_sixth04_X.size(); ++i) {
        fourthLineData.append(QPointF(data_addLine_sixth04_X[i], data_addLine_sixth04_Y[i]));
    }
    allData.append(fourthLineData);

    // 第五组数据
    QVector<QPointF> fifthLineData;
    for (int i = 0; i < data_addLine_sixth05_X.size(); ++i) {
        fifthLineData.append(QPointF(data_addLine_sixth05_X[i], data_addLine_sixth05_Y[i]));
    }
    allData.append(fifthLineData);

    // 第六组数据
    QVector<QPointF> sixthLineData;
    for (int i = 0; i < data_addLine_sixth06_X.size(); ++i) {
        sixthLineData.append(QPointF(data_addLine_sixth06_X[i], data_addLine_sixth06_Y[i]));
    }
    allData.append(sixthLineData);

    // 将数据传递给 MultiLineChartView
    multilinechartview6->setData(allData);
}


void Widget::on_btn_addLine_check_1_clicked()
{
    // 检查 data_addLine_first01_X 和 data_addLine_first01_Y 是否有内容
    if (!data_addLine_first01_X.isEmpty() && !data_addLine_first01_Y.isEmpty()) {
        QMessageBox::information(this, "检查结果", "数组已被填充");
    } else if (!data_addLine_first01_X.isEmpty() && data_addLine_first01_Y.isEmpty()) {
        QMessageBox::critical(this, "错误", "缺少 Y 数据");
    } else if (data_addLine_first01_X.isEmpty() && !data_addLine_first01_Y.isEmpty()) {
        QMessageBox::critical(this, "错误", "缺少 X 数据");
    } else {
        QMessageBox::warning(this, "警告", "未赋值");
    }
}


void Widget::on_btn_addLine_check_2_clicked()
{
    QString result;

    // 检查第一组数据
    if (!data_addLine_second01_X.isEmpty() && !data_addLine_second01_Y.isEmpty()) {
        result += "第一组数据已被填充\n";
    } else if (!data_addLine_second01_X.isEmpty() && data_addLine_second01_Y.isEmpty()) {
        result += "第一组数据缺少 Y 数据\n";
    } else if (data_addLine_second01_X.isEmpty() && !data_addLine_second01_Y.isEmpty()) {
        result += "第一组数据缺少 X 数据\n";
    } else {
        result += "第一组数据未赋值\n";
    }

    // 检查第二组数据
    if (!data_addLine_second02_X.isEmpty() && !data_addLine_second02_Y.isEmpty()) {
        result += "第二组数据已被填充\n";
    } else if (!data_addLine_second02_X.isEmpty() && data_addLine_second02_Y.isEmpty()) {
        result += "第二组数据缺少 Y 数据\n";
    } else if (data_addLine_second02_X.isEmpty() && !data_addLine_second02_Y.isEmpty()) {
        result += "第二组数据缺少 X 数据\n";
    } else {
        result += "第二组数据未赋值\n";
    }

    // 弹出汇总结果
    QMessageBox::information(this, "检查结果", result.trimmed());
}

void Widget::on_btn_addLine_check_3_clicked()
{
    QString result;

    // 检查第一组数据
    if (!data_addLine_third01_X.isEmpty() && !data_addLine_third01_Y.isEmpty()) {
        result += "第一组数据已被填充\n";
    } else if (!data_addLine_third01_X.isEmpty() && data_addLine_third01_Y.isEmpty()) {
        result += "第一组数据缺少 Y 数据\n";
    } else if (data_addLine_third01_X.isEmpty() && !data_addLine_third01_Y.isEmpty()) {
        result += "第一组数据缺少 X 数据\n";
    } else {
        result += "第一组数据未赋值\n";
    }

    // 检查第二组数据
    if (!data_addLine_third02_X.isEmpty() && !data_addLine_third02_Y.isEmpty()) {
        result += "第二组数据已被填充\n";
    } else if (!data_addLine_third02_X.isEmpty() && data_addLine_third02_Y.isEmpty()) {
        result += "第二组数据缺少 Y 数据\n";
    } else if (data_addLine_third02_X.isEmpty() && !data_addLine_third02_Y.isEmpty()) {
        result += "第二组数据缺少 X 数据\n";
    } else {
        result += "第二组数据未赋值\n";
    }

    // 检查第三组数据
    if (!data_addLine_third03_X.isEmpty() && !data_addLine_third03_Y.isEmpty()) {
        result += "第三组数据已被填充\n";
    } else if (!data_addLine_third03_X.isEmpty() && data_addLine_third03_Y.isEmpty()) {
        result += "第三组数据缺少 Y 数据\n";
    } else if (data_addLine_third03_X.isEmpty() && !data_addLine_third03_Y.isEmpty()) {
        result += "第三组数据缺少 X 数据\n";
    } else {
        result += "第三组数据未赋值\n";
    }

    // 弹出汇总结果
    QMessageBox::information(this, "检查结果", result.trimmed());
}


void Widget::on_btn_addLine_check_4_clicked()
{
    QString result;

    // 检查第一组数据
    if (!data_addLine_forth01_X.isEmpty() && !data_addLine_forth01_Y.isEmpty()) {
        result += "第一组数据已被填充\n";
    } else if (!data_addLine_forth01_X.isEmpty() && data_addLine_forth01_Y.isEmpty()) {
        result += "第一组数据缺少 Y 数据\n";
    } else if (data_addLine_forth01_X.isEmpty() && !data_addLine_forth01_Y.isEmpty()) {
        result += "第一组数据缺少 X 数据\n";
    } else {
        result += "第一组数据未赋值\n";
    }

    // 检查第二组数据
    if (!data_addLine_forth02_X.isEmpty() && !data_addLine_forth02_Y.isEmpty()) {
        result += "第二组数据已被填充\n";
    } else if (!data_addLine_forth02_X.isEmpty() && data_addLine_forth02_Y.isEmpty()) {
        result += "第二组数据缺少 Y 数据\n";
    } else if (data_addLine_forth02_X.isEmpty() && !data_addLine_forth02_Y.isEmpty()) {
        result += "第二组数据缺少 X 数据\n";
    } else {
        result += "第二组数据未赋值\n";
    }

    // 检查第三组数据
    if (!data_addLine_forth03_X.isEmpty() && !data_addLine_forth03_Y.isEmpty()) {
        result += "第三组数据已被填充\n";
    } else if (!data_addLine_forth03_X.isEmpty() && data_addLine_forth03_Y.isEmpty()) {
        result += "第三组数据缺少 Y 数据\n";
    } else if (data_addLine_forth03_X.isEmpty() && !data_addLine_forth03_Y.isEmpty()) {
        result += "第三组数据缺少 X 数据\n";
    } else {
        result += "第三组数据未赋值\n";
    }

    // 检查第四组数据
    if (!data_addLine_forth04_X.isEmpty() && !data_addLine_forth04_Y.isEmpty()) {
        result += "第四组数据已被填充\n";
    } else if (!data_addLine_forth04_X.isEmpty() && data_addLine_forth04_Y.isEmpty()) {
        result += "第四组数据缺少 Y 数据\n";
    } else if (data_addLine_forth04_X.isEmpty() && !data_addLine_forth04_Y.isEmpty()) {
        result += "第四组数据缺少 X 数据\n";
    } else {
        result += "第四组数据未赋值\n";
    }

    // 弹出汇总结果
    QMessageBox::information(this, "检查结果", result.trimmed());
}



void Widget::on_btn_addLine_check_5_clicked()
{
    QString result;

    // 检查第一组数据
    if (!data_addLine_fifth01_X.isEmpty() && !data_addLine_fifth01_Y.isEmpty()) {
        result += "第一组数据已被填充\n";
    } else if (!data_addLine_fifth01_X.isEmpty() && data_addLine_fifth01_Y.isEmpty()) {
        result += "第一组数据缺少 Y 数据\n";
    } else if (data_addLine_fifth01_X.isEmpty() && !data_addLine_fifth01_Y.isEmpty()) {
        result += "第一组数据缺少 X 数据\n";
    } else {
        result += "第一组数据未赋值\n";
    }

    // 检查第二组数据
    if (!data_addLine_fifth02_X.isEmpty() && !data_addLine_fifth02_Y.isEmpty()) {
        result += "第二组数据已被填充\n";
    } else if (!data_addLine_fifth02_X.isEmpty() && data_addLine_fifth02_Y.isEmpty()) {
        result += "第二组数据缺少 Y 数据\n";
    } else if (data_addLine_fifth02_X.isEmpty() && !data_addLine_fifth02_Y.isEmpty()) {
        result += "第二组数据缺少 X 数据\n";
    } else {
        result += "第二组数据未赋值\n";
    }

    // 检查第三组数据
    if (!data_addLine_fifth03_X.isEmpty() && !data_addLine_fifth03_Y.isEmpty()) {
        result += "第三组数据已被填充\n";
    } else if (!data_addLine_fifth03_X.isEmpty() && data_addLine_fifth03_Y.isEmpty()) {
        result += "第三组数据缺少 Y 数据\n";
    } else if (data_addLine_fifth03_X.isEmpty() && !data_addLine_fifth03_Y.isEmpty()) {
        result += "第三组数据缺少 X 数据\n";
    } else {
        result += "第三组数据未赋值\n";
    }

    // 检查第四组数据
    if (!data_addLine_fifth04_X.isEmpty() && !data_addLine_fifth04_Y.isEmpty()) {
        result += "第四组数据已被填充\n";
    } else if (!data_addLine_fifth04_X.isEmpty() && data_addLine_fifth04_Y.isEmpty()) {
        result += "第四组数据缺少 Y 数据\n";
    } else if (data_addLine_fifth04_X.isEmpty() && !data_addLine_fifth04_Y.isEmpty()) {
        result += "第四组数据缺少 X 数据\n";
    } else {
        result += "第四组数据未赋值\n";
    }

    // 检查第五组数据
    if (!data_addLine_fifth05_X.isEmpty() && !data_addLine_fifth05_Y.isEmpty()) {
        result += "第五组数据已被填充\n";
    } else if (!data_addLine_fifth05_X.isEmpty() && data_addLine_fifth05_Y.isEmpty()) {
        result += "第五组数据缺少 Y 数据\n";
    } else if (data_addLine_fifth05_X.isEmpty() && !data_addLine_fifth05_Y.isEmpty()) {
        result += "第五组数据缺少 X 数据\n";
    } else {
        result += "第五组数据未赋值\n";
    }

    // 弹出汇总结果
    QMessageBox::information(this, "检查结果", result.trimmed());
}


void Widget::on_btn_addLine_check_6_clicked()
{
    QString result;

    // 检查第一组数据
    if (!data_addLine_sixth01_X.isEmpty() && !data_addLine_sixth01_Y.isEmpty()) {
        result += "第一组数据已被填充\n";
    } else if (!data_addLine_sixth01_X.isEmpty() && data_addLine_sixth01_Y.isEmpty()) {
        result += "第一组数据缺少 Y 数据\n";
    } else if (data_addLine_sixth01_X.isEmpty() && !data_addLine_sixth01_Y.isEmpty()) {
        result += "第一组数据缺少 X 数据\n";
    } else {
        result += "第一组数据未赋值\n";
    }

    // 检查第二组数据
    if (!data_addLine_sixth02_X.isEmpty() && !data_addLine_sixth02_Y.isEmpty()) {
        result += "第二组数据已被填充\n";
    } else if (!data_addLine_sixth02_X.isEmpty() && data_addLine_sixth02_Y.isEmpty()) {
        result += "第二组数据缺少 Y 数据\n";
    } else if (data_addLine_sixth02_X.isEmpty() && !data_addLine_sixth02_Y.isEmpty()) {
        result += "第二组数据缺少 X 数据\n";
    } else {
        result += "第二组数据未赋值\n";
    }

    // 检查第三组数据
    if (!data_addLine_sixth03_X.isEmpty() && !data_addLine_sixth03_Y.isEmpty()) {
        result += "第三组数据已被填充\n";
    } else if (!data_addLine_sixth03_X.isEmpty() && data_addLine_sixth03_Y.isEmpty()) {
        result += "第三组数据缺少 Y 数据\n";
    } else if (data_addLine_sixth03_X.isEmpty() && !data_addLine_sixth03_Y.isEmpty()) {
        result += "第三组数据缺少 X 数据\n";
    } else {
        result += "第三组数据未赋值\n";
    }

    // 检查第四组数据
    if (!data_addLine_sixth04_X.isEmpty() && !data_addLine_sixth04_Y.isEmpty()) {
        result += "第四组数据已被填充\n";
    } else if (!data_addLine_sixth04_X.isEmpty() && data_addLine_sixth04_Y.isEmpty()) {
        result += "第四组数据缺少 Y 数据\n";
    } else if (data_addLine_sixth04_X.isEmpty() && !data_addLine_sixth04_Y.isEmpty()) {
        result += "第四组数据缺少 X 数据\n";
    } else {
        result += "第四组数据未赋值\n";
    }

    // 检查第五组数据
    if (!data_addLine_sixth05_X.isEmpty() && !data_addLine_sixth05_Y.isEmpty()) {
        result += "第五组数据已被填充\n";
    } else if (!data_addLine_sixth05_X.isEmpty() && data_addLine_sixth05_Y.isEmpty()) {
        result += "第五组数据缺少 Y 数据\n";
    } else if (data_addLine_sixth05_X.isEmpty() && !data_addLine_sixth05_Y.isEmpty()) {
        result += "第五组数据缺少 X 数据\n";
    } else {
        result += "第五组数据未赋值\n";
    }

    // 检查第六组数据
    if (!data_addLine_sixth06_X.isEmpty() && !data_addLine_sixth06_Y.isEmpty()) {
        result += "第六组数据已被填充\n";
    } else if (!data_addLine_sixth06_X.isEmpty() && data_addLine_sixth06_Y.isEmpty()) {
        result += "第六组数据缺少 Y 数据\n";
    } else if (data_addLine_sixth06_X.isEmpty() && !data_addLine_sixth06_Y.isEmpty()) {
        result += "第六组数据缺少 X 数据\n";
    } else {
        result += "第六组数据未赋值\n";
    }

    // 弹出汇总结果
    QMessageBox::information(this, "检查结果", result.trimmed());
}

```