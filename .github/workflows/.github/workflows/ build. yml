#import <UIKit/UIKit.h>
#import <Foundation/Foundation.h>
#import <QuartzCore/QuartzCore.h>

@interface FloatingModMenu : UIView
@property (nonatomic, strong) UILabel *statusLabel;
@property (nonatomic, strong) UIButton *autoBtn;
@property (nonatomic, strong) UIButton *espBtn;
@property (nonatomic, strong) UIButton *reviveBtn;
@property (nonatomic, strong) UIButton *comboBtn;
@property (nonatomic, assign) BOOL isAutoSolverOn;
@property (nonatomic, assign) BOOL isEspOn;
@property (nonatomic, assign) BOOL isReviveOn;
@property (nonatomic, assign) NSInteger comboCount;
@property (nonatomic, strong) NSString *currentMode;
@property (nonatomic, strong) NSString *targetScore;
@property (nonatomic, strong) NSArray<UIButton *> *targetButtons;
@property (nonatomic, strong) NSArray<UIButton *> *speedButtons;
@property (nonatomic, strong) CAShapeLayer *espLayer;
@end

static FloatingModMenu *g_menuView = nil;

@implementation FloatingModMenu

- (instancetype)initWithFrame:(CGRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        self.isAutoSolverOn = NO;
        self.isEspOn = YES;
        self.isReviveOn = NO;
        self.comboCount = 20;
        self.currentMode = @"ADVENTURE";
        self.targetScore = @"∞";

        self.backgroundColor = [UIColor colorWithRed:14/255.0 green:21/255.0 blue:34/255.0 alpha:0.96];
        self.layer.cornerRadius = 14.0;
        self.layer.borderWidth = 1.2;
        self.layer.borderColor = [UIColor colorWithRed:48/255.0 green:72/255.0 blue:108/255.0 alpha:1.0].CGColor;
        self.layer.shadowColor = [UIColor blackColor].CGColor;
        self.layer.shadowOpacity = 0.65;
        self.layer.shadowRadius = 12.0;
        self.layer.shadowOffset = CGSizeMake(0, 6);
        self.clipsToBounds = YES;

        UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handlePan:)];
        [self addGestureRecognizer:pan];

        [self setupUI];
    }
    return self;
}

- (void)setupUI {
    CGFloat width = self.bounds.size.width;
    CGFloat pad = 10.0;
    CGFloat contentW = width - pad * 2;
    CGFloat y = 10.0;

    // 1. Header: MENU BY MINH ĐĂNG
    UILabel *titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(pad, y, contentW - 82, 20)];
    titleLabel.text = @"🧩 MENU BY MINH ĐĂNG";
    titleLabel.textColor = [UIColor colorWithRed:92/255.0 green:214/255.0 blue:255/255.0 alpha:1.0];
    titleLabel.font = [UIFont boldSystemFontOfSize:12.0];
    titleLabel.adjustsFontSizeToFitWidth = YES;
    titleLabel.minimumScaleFactor = 0.75;
    [self addSubview:titleLabel];

    UIButton *hideBtn = [UIButton buttonWithType:UIButtonTypeSystem];
    hideBtn.frame = CGRectMake(width - pad - 78, y, 78, 20);
    [hideBtn setTitle:@"Ẩn (3-Ngón)" forState:UIControlStateNormal];
    [hideBtn setTitleColor:[UIColor colorWithWhite:0.75 alpha:1.0] forState:UIControlStateNormal];
    hideBtn.titleLabel.font = [UIFont systemFontOfSize:10.5 weight:UIFontWeightMedium];
    hideBtn.contentHorizontalAlignment = UIControlContentHorizontalAlignmentRight;
    [hideBtn addTarget:self action:@selector(toggleMenuVisibility) forControlEvents:UIControlEventTouchUpInside];
    [self addSubview:hideBtn];

    y += 22;

    // 2. Dòng thông tin trạng thái
    self.statusLabel = [[UILabel alloc] initWithFrame:CGRectMake(pad, y, contentW, 18)];
    self.statusLabel.text = @"🎯 Gạch #1 -> Ô (6,1) • Nổ 1 hàng • Điểm 407";
    self.statusLabel.textColor = [UIColor colorWithWhite:0.88 alpha:1.0];
    self.statusLabel.font = [UIFont systemFontOfSize:11];
    self.statusLabel.adjustsFontSizeToFitWidth = YES;
    self.statusLabel.minimumScaleFactor = 0.75;
    [self addSubview:self.statusLabel];

    y += 24;

    // 3. Hàng Toggle chính (Auto Solver & Mode)
    CGFloat halfW = (contentW - 8) / 2.0;
    self.autoBtn = [self createButtonWithTitle:@"▶ AUTO SOLVER: TẮT"
                                         frame:CGRectMake(pad, y, halfW, 32)
                                         color:[UIColor colorWithRed:40/255.0 green:52/255.0 blue:72/255.0 alpha:1.0]];
    [self.autoBtn addTarget:self action:@selector(onToggleAuto:) forControlEvents:UIControlEventTouchUpInside];
    [self addSubview:self.autoBtn];

    UIButton *modeBtn = [self createButtonWithTitle:@"🗺 MODE: ADVENTURE"
                                              frame:CGRectMake(pad + halfW + 8, y, halfW, 32)
                                              color:[UIColor colorWithRed:78/255.0 green:62/255.0 blue:136/255.0 alpha:1.0]];
    [modeBtn addTarget:self action:@selector(onToggleMode:) forControlEvents:UIControlEventTouchUpInside];
    [self addSubview:modeBtn];

    y += 40;

    // 4. Hàng chọn mức điểm mục tiêu
    UILabel *targetLbl = [[UILabel alloc] initWithFrame:CGRectMake(pad, y, contentW, 15)];
    targetLbl.text = @"🎯 MỨC ĐIỂM MỤC TIÊU (CHỌN ĐỂ TỰ DỪNG / ĐẶT ĐIỂM)";
    targetLbl.textColor = [UIColor colorWithWhite:0.68 alpha:1.0];
    targetLbl.font = [UIFont boldSystemFontOfSize:9.5];
    targetLbl.adjustsFontSizeToFitWidth = YES;
    targetLbl.minimumScaleFactor = 0.75;
    [self addSubview:targetLbl];

    y += 19;

    NSArray *targets = @[@"10K", @"50K", @"100K", @"500K", @"1M", @"∞"];
    NSMutableArray *tBtns = [NSMutableArray array];
    CGFloat itemW = (contentW - (targets.count - 1) * 5) / targets.count;
    for (NSInteger i = 0; i < targets.count; i++) {
        BOOL isSelected = [targets[i] isEqualToString:self.targetScore];
        UIColor *bg = isSelected
            ? [UIColor colorWithRed:32/255.0 green:135/255.0 blue:210/255.0 alpha:1.0]
            : [UIColor colorWithRed:34/255.0 green:45/255.0 blue:65/255.0 alpha:1.0];
        UIButton *btn = [self createButtonWithTitle:targets[i]
                                              frame:CGRectMake(pad + i * (itemW + 5), y, itemW, 26)
                                              color:bg];
        btn.titleLabel.font = [UIFont boldSystemFontOfSize:11];
        [btn addTarget:self action:@selector(onSelectTarget:) forControlEvents:UIControlEventTouchUpInside];
        [self addSubview:btn];
        [tBtns addObject:btn];
    }
    self.targetButtons = tBtns;

    y += 34;

    // 5. Hàng nút hành động điểm
    CGFloat thirdW = (contentW - 12) / 3.0;
    UIButton *setScoreBtn = [self createButtonWithTitle:@"💎 Đặt Điểm Này"
                                                  frame:CGRectMake(pad, y, thirdW, 30)
                                                  color:[UIColor colorWithRed:102/255.0 green:72/255.0 blue:214/255.0 alpha:1.0]];
    UIButton *addScoreBtn = [self createButtonWithTitle:@"⚡ +25K Điểm"
                                                  frame:CGRectMake(pad + thirdW + 6, y, thirdW, 30)
                                                  color:[UIColor colorWithRed:204/255.0 green:126/255.0 blue:24/255.0 alpha:1.0]];
    UIButton *stepBtn     = [self createButtonWithTitle:@"🎯 Giải 1 Nước"
                                                  frame:CGRectMake(pad + (thirdW + 6) * 2, y, thirdW, 30)
                                                  color:[UIColor colorWithRed:30/255.0 green:118/255.0 blue:190/255.0 alpha:1.0]];
    [setScoreBtn addTarget:self action:@selector(onActionToast:) forControlEvents:UIControlEventTouchUpInside];
    [addScoreBtn addTarget:self action:@selector(onActionToast:) forControlEvents:UIControlEventTouchUpInside];
    [stepBtn addTarget:self action:@selector(onActionToast:) forControlEvents:UIControlEventTouchUpInside];
    [self addSubview:setScoreBtn];
    [self addSubview:addScoreBtn];
    [self addSubview:stepBtn];

    y += 38;

    // 6. Hàng chọn Tốc độ
    UILabel *speedLbl = [[UILabel alloc] initWithFrame:CGRectMake(pad, y + 4, 54, 20)];
    speedLbl.text = @"TỐC ĐỘ:";
    speedLbl.textColor = [UIColor colorWithWhite:0.78 alpha:1.0];
    speedLbl.font = [UIFont boldSystemFontOfSize:10.5];
    [self addSubview:speedLbl];

    NSArray *speeds = @[@"1x", @"2x", @"5x", @"⚡ Turbo"];
    NSMutableArray *sBtns = [NSMutableArray array];
    CGFloat speedAreaW = contentW - 56;
    CGFloat sBtnW = (speedAreaW - (speeds.count - 1) * 5) / speeds.count;
    for (NSInteger i = 0; i < speeds.count; i++) {
        UIColor *bg = (i == 3)
            ? [UIColor colorWithRed:208/255.0 green:130/255.0 blue:24/255.0 alpha:1.0]
            : [UIColor colorWithRed:34/255.0 green:45/255.0 blue:65/255.0 alpha:1.0];
        UIButton *btn = [self createButtonWithTitle:speeds[i]
                                              frame:CGRectMake(pad + 56 + i * (sBtnW + 5), y, sBtnW, 26)
                                              color:bg];
        btn.tag = i;
        btn.titleLabel.font = [UIFont boldSystemFontOfSize:10.5];
        if (i == 3) {
            btn.layer.borderWidth = 1.2;
            btn.layer.borderColor = [UIColor colorWithWhite:1.0 alpha:0.85].CGColor;
        }
        [btn addTarget:self action:@selector(onSelectSpeed:) forControlEvents:UIControlEventTouchUpInside];
        [self addSubview:btn];
        [sBtns addObject:btn];
    }
    self.speedButtons = sBtns;

    y += 34;

    // 7. Hàng chức năng bàn cờ
    UIButton *bombBtn = [self createButtonWithTitle:@"💣 Nổ Bàn Cờ"
                                              frame:CGRectMake(pad, y, thirdW, 30)
                                              color:[UIColor colorWithRed:188/255.0 green:48/255.0 blue:52/255.0 alpha:1.0]];
    UIButton *rerollBtn = [self createButtonWithTitle:@"🔄 Đổi 3 Gạch"
                                                frame:CGRectMake(pad + thirdW + 6, y, thirdW, 30)
                                                color:[UIColor colorWithRed:42/255.0 green:125/255.0 blue:198/255.0 alpha:1.0]];
    UIButton *undoBtn = [self createButtonWithTitle:@"↩️ Hoàn Tác"
                                              frame:CGRectMake(pad + (thirdW + 6) * 2, y, thirdW, 30)
                                              color:[UIColor colorWithRed:65/255.0 green:78/255.0 blue:102/255.0 alpha:1.0]];
    [bombBtn addTarget:self action:@selector(onActionToast:) forControlEvents:UIControlEventTouchUpInside];
    [rerollBtn addTarget:self action:@selector(onActionToast:) forControlEvents:UIControlEventTouchUpInside];
    [undoBtn addTarget:self action:@selector(onActionToast:) forControlEvents:UIControlEventTouchUpInside];
    [self addSubview:bombBtn];
    [self addSubview:rerollBtn];
    [self addSubview:undoBtn];

    y += 38;

    // 8. Hàng ESP / Combo / Cứu thua
    self.espBtn = [self createButtonWithTitle:@"👁 ESP: BẬT"
                                        frame:CGRectMake(pad, y, thirdW, 30)
                                        color:[UIColor colorWithRed:24/255.0 green:142/255.0 blue:118/255.0 alpha:1.0]];
    [self.espBtn addTarget:self action:@selector(onToggleEsp:) forControlEvents:UIControlEventTouchUpInside];

    self.comboBtn = [self createButtonWithTitle:@"🔥 Combo: x20"
                                          frame:CGRectMake(pad + thirdW + 6, y, thirdW, 30)
                                          color:[UIColor colorWithRed:216/255.0 green:106/255.0 blue:34/255.0 alpha:1.0]];
    [self.comboBtn addTarget:self action:@selector(onCycleCombo:) forControlEvents:UIControlEventTouchUpInside];

    self.reviveBtn = [self createButtonWithTitle:@"🛡 Cứu Thua: TẮT"
                                           frame:CGRectMake(pad + (thirdW + 6) * 2, y, thirdW, 30)
                                           color:[UIColor colorWithRed:65/255.0 green:78/255.0 blue:102/255.0 alpha:1.0]];
    [self.reviveBtn addTarget:self action:@selector(onToggleRevive:) forControlEvents:UIControlEventTouchUpInside];

    [self addSubview:self.espBtn];
    [self addSubview:self.comboBtn];
    [self addSubview:self.reviveBtn];

    y += 36;

    // 9. Footer hướng dẫn
    UILabel *tipLabel = [[UILabel alloc] initWithFrame:CGRectMake(pad, y, contentW, 28)];
    tipLabel.text = @"💡 Mẹo: Chạm 3 lần (3 ngón tay hoặc 3 lần vào mép trên màn hình) để ẨN / HIỆN menu bất cứ lúc nào.";
    tipLabel.textColor = [UIColor colorWithWhite:0.65 alpha:1.0];
    tipLabel.font = [UIFont systemFontOfSize:9.5];
    tipLabel.numberOfLines = 2;
    [self addSubview:tipLabel];
}

- (UIButton *)createButtonWithTitle:(NSString *)title frame:(CGRect)frame color:(UIColor *)color {
    UIButton *btn = [UIButton buttonWithType:UIButtonTypeCustom];
    btn.frame = frame;
    btn.backgroundColor = color;
    btn.layer.cornerRadius = 7.0;
    [btn setTitle:title forState:UIControlStateNormal];
    [btn setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
    btn.titleLabel.font = [UIFont boldSystemFontOfSize:11];
    btn.titleLabel.adjustsFontSizeToFitWidth = YES;
    btn.titleLabel.minimumScaleFactor = 0.72;
    return btn;
}

- (void)setupEspOverlayInWindow:(UIWindow *)window {
    if (self.espLayer || !window) return;

    CAShapeLayer *layer = [CAShapeLayer layer];
    layer.frame = window.bounds;
    layer.strokeColor = [UIColor colorWithRed:64/255.0 green:224/255.0 blue:168/255.0 alpha:0.9].CGColor;
    layer.fillColor = [UIColor clearColor].CGColor;
    layer.lineWidth = 1.8;
    layer.lineDashPattern = @[@6, @4];

    UIBezierPath *path = [UIBezierPath bezierPath];
    CGFloat w = window.bounds.size.width;
    CGFloat h = window.bounds.size.height;
    [path moveToPoint:CGPointMake(w * 0.22, h * 0.44)];
    [path addLineToPoint:CGPointMake(w * 0.36, h * 0.68)];

    layer.path = path.CGPath;
    layer.hidden = !self.isEspOn;
    [window.layer addSublayer:layer];
    self.espLayer = layer;
}

#pragma mark - Actions

- (void)onToggleAuto:(UIButton *)sender {
    self.isAutoSolverOn = !self.isAutoSolverOn;
    if (self.isAutoSolverOn) {
        [sender setTitle:@"⏸ AUTO SOLVER: BẬT" forState:UIControlStateNormal];
        sender.backgroundColor = [UIColor colorWithRed:28/255.0 green:155/255.0 blue:95/255.0 alpha:1.0];
        self.statusLabel.text = [NSString stringWithFormat:@"⚡ Auto Solver đang chạy • Mục tiêu: %@", self.targetScore];
    } else {
        [sender setTitle:@"▶ AUTO SOLVER: TẮT" forState:UIControlStateNormal];
        sender.backgroundColor = [UIColor colorWithRed:40/255.0 green:52/255.0 blue:72/255.0 alpha:1.0];
        self.statusLabel.text = @"🎯 Gạch #1 -> Ô (6,1) • Nổ 1 hàng • Điểm 407";
    }
}

- (void)onToggleMode:(UIButton *)sender {
    if ([self.currentMode isEqualToString:@"ADVENTURE"]) {
        self.currentMode = @"CLASSIC";
        [sender setTitle:@"🗺 MODE: CLASSIC" forState:UIControlStateNormal];
    } else if ([self.currentMode isEqualToString:@"CLASSIC"]) {
        self.currentMode = @"ENDLESS";
        [sender setTitle:@"🗺 MODE: ENDLESS" forState:UIControlStateNormal];
    } else {
        self.currentMode = @"ADVENTURE";
        [sender setTitle:@"🗺 MODE: ADVENTURE" forState:UIControlStateNormal];
    }
}

- (void)onSelectTarget:(UIButton *)sender {
    self.targetScore = [sender titleForState:UIControlStateNormal];
    for (UIButton *b in self.targetButtons) {
        BOOL active = [[b titleForState:UIControlStateNormal] isEqualToString:self.targetScore];
        b.backgroundColor = active
            ? [UIColor colorWithRed:32/255.0 green:135/255.0 blue:210/255.0 alpha:1.0]
            : [UIColor colorWithRed:34/255.0 green:45/255.0 blue:65/255.0 alpha:1.0];
    }
    self.statusLabel.text = [NSString stringWithFormat:@"🎯 Mục tiêu đã chọn: %@", self.targetScore];
}

- (void)onSelectSpeed:(UIButton *)sender {
    for (UIButton *b in self.speedButtons) {
        b.layer.borderWidth = 0;
        b.backgroundColor = [UIColor colorWithRed:34/255.0 green:45/255.0 blue:65/255.0 alpha:1.0];
    }
    sender.layer.borderWidth = 1.2;
    sender.layer.borderColor = [UIColor colorWithWhite:1.0 alpha:0.85].CGColor;
    sender.backgroundColor = (sender.tag == 3)
        ? [UIColor colorWithRed:208/255.0 green:130/255.0 blue:24/255.0 alpha:1.0]
        : [UIColor colorWithRed:32/255.0 green:135/255.0 blue:210/255.0 alpha:1.0];
    self.statusLabel.text = [NSString stringWithFormat:@"⚡ Tốc độ: %@", [sender titleForState:UIControlStateNormal]];
}

- (void)onToggleEsp:(UIButton *)sender {
    self.isEspOn = !self.isEspOn;
    self.espLayer.hidden = !self.isEspOn;
    if (self.isEspOn) {
        [sender setTitle:@"👁 ESP: BẬT" forState:UIControlStateNormal];
        sender.backgroundColor = [UIColor colorWithRed:24/255.0 green:142/255.0 blue:118/255.0 alpha:1.0];
    } else {
        [sender setTitle:@"👁 ESP: TẮT" forState:UIControlStateNormal];
        sender.backgroundColor = [UIColor colorWithRed:65/255.0 green:78/255.0 blue:102/255.0 alpha:1.0];
    }
}

- (void)onCycleCombo:(UIButton *)sender {
    self.comboCount = (self.comboCount >= 50) ? 10 : self.comboCount + 10;
    [sender setTitle:[NSString stringWithFormat:@"🔥 Combo: x%ld", (long)self.comboCount] forState:UIControlStateNormal];
}

- (void)onToggleRevive:(UIButton *)sender {
    self.isReviveOn = !self.isReviveOn;
    if (self.isReviveOn) {
        [sender setTitle:@"🛡 Cứu Thua: BẬT" forState:UIControlStateNormal];
        sender.backgroundColor = [UIColor colorWithRed:28/255.0 green:155/255.0 blue:95/255.0 alpha:1.0];
    } else {
        [sender setTitle:@"🛡 Cứu Thua: TẮT" forState:UIControlStateNormal];
        sender.backgroundColor = [UIColor colorWithRed:65/255.0 green:78/255.0 blue:102/255.0 alpha:1.0];
    }
}

- (void)onActionToast:(UIButton *)sender {
    NSString *name = [sender titleForState:UIControlStateNormal];
    self.statusLabel.text = [NSString stringWithFormat:@"✅ Đã kích hoạt UI: %@", name];
    sender.alpha = 0.65;
    [UIView animateWithDuration:0.15 animations:^{
        sender.alpha = 1.0;
    }];
}

- (void)handlePan:(UIPanGestureRecognizer *)pan {
    CGPoint translation = [pan translationInView:self.superview];
    self.center = CGPointMake(self.center.x + translation.x, self.center.y + translation.y);
    [pan setTranslation:CGPointZero inView:self.superview];
}

- (void)toggleMenuVisibility {
    self.hidden = !self.hidden;
}

@end

#pragma mark - Constructor & Window Injection

static UIWindow *GetMainWindow(void) {
    for (UIScene *scene in [UIApplication sharedApplication].connectedScenes) {
        if ([scene isKindOfClass:[UIWindowScene class]]) {
            UIWindowScene *windowScene = (UIWindowScene *)scene;
            for (UIWindow *window in windowScene.windows) {
                if (window.isKeyWindow) return window;
            }
            if (windowScene.windows.count > 0) return windowScene.windows.firstObject;
        }
    }
    return [UIApplication sharedApplication].windows.firstObject;
}

static void SetupFloatingMenu(void) {
    UIWindow *mainWindow = GetMainWindow();
    if (!mainWindow || g_menuView) return;

    CGFloat screenW = mainWindow.bounds.size.width;
    CGFloat screenH = mainWindow.bounds.size.height;
    CGFloat menuW = MIN(screenW - 20.0, 366.0);
    CGFloat menuH = 318.0;

    g_menuView = [[FloatingModMenu alloc] initWithFrame:CGRectMake((screenW - menuW) / 2.0, screenH - menuH - 32.0, menuW, menuH)];
    [g_menuView setupEspOverlayInWindow:mainWindow];
    [mainWindow addSubview:g_menuView];

    UITapGestureRecognizer *threeFingerTap = [[UITapGestureRecognizer alloc] initWithTarget:g_menuView action:@selector(toggleMenuVisibility)];
    threeFingerTap.numberOfTouchesRequired = 3;
    threeFingerTap.cancelsTouchesInView = NO;
    [mainWindow addGestureRecognizer:threeFingerTap];
}

__attribute__((constructor))
static void InitializeDylib(void) {
    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(2.5 * NSEC_PER_SEC)), dispatch_get_main_queue(), ^{
        SetupFloatingMenu();
    });
}
