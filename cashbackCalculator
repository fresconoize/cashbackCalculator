'use strict';

(() => {
  if (window.__promoToolLoaded) return;
  window.__promoToolLoaded = true;

  const bar = (m) => {
    const e = document.createElement("div");
    e.textContent = m;
    e.style.cssText = "position:fixed;top:0;left:0;right:0;z-index:999999;background:#111;color:#fff;padding:12px;text-align:center;font-size:14px";
    document.body.appendChild(e);
    setTimeout(() => e.remove(), 2500);
  };

  const sleep = (t) => new Promise((r) => setTimeout(r, t));

  const waitRows = async () => {
    for (let i = 0; i < 100; i++) {
      const rows = [...document.querySelectorAll("tr[class^='_table-row_']")];
      if (rows.length) return rows;
      await sleep(200);
    }
    return null;
  };

  const detectPromo = (tds) => {
    const priceCell = tds[3], finalCell = tds[4];
    const priceBlack = priceCell?.querySelector(".price-item.price-base:not(.price-cross):not(.price-red)");
    const priceCrossBlack = priceCell?.querySelector(".price-cross.price-item.price-base:not(.price-red)");
    const priceCrossRed = priceCell?.querySelector(".price-cross.price-item.price-base.price-red");
    const finalBlack = finalCell?.querySelector(".price:not(.price-red)");
    const finalRed = finalCell?.querySelector(".price.price-red");
    const c1 = !!(priceBlack && finalBlack);
    const c2 = !!(priceCrossBlack && finalRed);
    const c3 = !!(priceCrossRed && finalBlack);
    const c4 = !!(priceCrossBlack && finalBlack);
    return { hasPromo: c3 || c4, matched: c1 || c2 || c3 || c4 };
  };

  const getNumberFromSum = (str) => {
    const cleaned = str.replace(/\s/g, '').replace(/[^0-9.,]/g, '').replace(',', '.');
    const num = parseFloat(cleaned);
    return isNaN(num) ? 0 : num;
  };

  const removePillsByType = (container, type) => {
    if (!container) return;
    const pills = container.querySelectorAll(`.promo-pill-${type}`);
    pills.forEach((p) => p.remove());
  };

  const removeTotalPill = (nameBlock) => {
    const totalDiv = nameBlock.parentNode?.querySelector('.promo-pill-total');
    if (totalDiv) totalDiv.remove();
  };

  const brandList = [
    { pattern: /Premiere of Taste/i, name: "Premiere of Taste" },
    { pattern: /La Fresh/i, name: "La Fresh" },
    { pattern: /Восточный гость/i, name: "Восточный гость" },
    { pattern: /EatMeat/i, name: "EatMeat" },
    { pattern: /Inspirato/i, name: "Inspirato" },
    { pattern: /М Кухня/i, name: "М Кухня" },
  ];

  const getBrandName = (title) => {
    for (let brand of brandList) {
      if (brand.pattern.test(title)) return brand.name;
    }
    return null;
  };

  const createPillContainer = (nameBlock) => {
    let container = nameBlock.parentNode?.querySelector('.promo-pills-container');
    if (!container) {
      container = document.createElement('div');
      container.className = 'promo-pills-container';
      container.style.cssText = "display:flex;flex-wrap:wrap;gap:6px;margin-top:0;align-items:center;";
      nameBlock.parentNode?.insertBefore(container, nameBlock.nextSibling);
    }
    return container;
  };

  const createPillWithAnimation = (text, type, container, delayMs) => {
    const pill = document.createElement('span');
    pill.className = `promo-pill-${type}`;
    pill.textContent = text;
    if (type === 'total') {
      pill.style.cssText = "background:linear-gradient(135deg,#ff7b7b 0%,#ff3d3d 25%,#ff1e01 60%,#e50000 100%);color:#fff;border-radius:16px;padding:2px 8px;font-size:12px;display:inline-block;white-space:nowrap;transform:scale(0);opacity:0;transition:transform 0.15s cubic-bezier(0.34,1.2,0.64,1),opacity 0.15s ease;margin-top:8px;box-shadow:0 2px 8px rgba(255,30,1,.3);";
    } else {
      pill.style.cssText = "background:#f0f0ef;color:#333232;border-radius:16px;padding:2px 8px;font-size:12px;display:inline-block;white-space:nowrap;transform:scale(0);opacity:0;transition:transform 0.15s cubic-bezier(0.34,1.2,0.64,1),opacity 0.15s ease;";
    }
    container.appendChild(pill);
    setTimeout(() => {
      pill.style.transform = 'scale(1)';
      pill.style.opacity = '1';
    }, delayMs);
    return pill;
  };

  const createTotalPillWithAnimation = (totalValue, container, nameBlock, delayMs) => {
    const pill = document.createElement('div');
    pill.className = 'promo-pill-total';
    pill.textContent = `Всего ${totalValue} бонусов`;
    pill.style.cssText = "background:linear-gradient(135deg,#ff7b7b 0%,#ff3d3d 25%,#ff1e01 60%,#e50000 100%);color:#fff;border-radius:16px;padding:2px 8px;font-size:12px;display:inline-block;white-space:nowrap;margin-bottom:6px;margin-top:8px;transform:scale(0);opacity:0;transition:transform 0.15s cubic-bezier(0.34,1.2,0.64,1),opacity 0.15s ease;box-shadow:0 2px 8px rgba(255,30,1,.3);";
    nameBlock.parentNode?.insertBefore(pill, container);
    setTimeout(() => {
      pill.style.transform = 'scale(1)';
      pill.style.opacity = '1';
    }, delayMs);
    return pill;
  };

  const createIndividualInput = (nameBlock, row) => {
    if (nameBlock.querySelector('.promo-tool-individual-input')) return;
    const inputDiv = document.createElement('div');
    inputDiv.className = 'promo-tool-individual-input';
    inputDiv.style.cssText = "margin-top:4px;";
    const input = document.createElement('input');
    input.type = 'number';
    input.placeholder = '% кешбэка по категории';
    input.step = 'any';
    input.style.cssText = "width:80px;padding:2px 6px;border:1px solid #ccc;border-radius:12px;font-size:11px;transition:all 0.2s ease;";
    input.addEventListener('focus', () => input.style.borderColor = '#ff1e01');
    input.addEventListener('blur', () => input.style.borderColor = '#ccc');
    inputDiv.appendChild(input);
    nameBlock.parentNode?.insertBefore(inputDiv, nameBlock.nextSibling);
    const container = createPillContainer(nameBlock);
    return inputDiv;
  };

  let activeTab = 'premium';
  let commonPercentage = 0;
  let summarySpan = null;
  let isCalculating = false;
  let isAnimating = false;
  let helpPanel = null;
  let helpContent = null;
  let helpVisible = false;
  let confirmBubble = null;

  const animateSummaryPanel = (newContent, newBackground, newColor) => {
    if (!summarySpan || isAnimating) return;
    isAnimating = true;
    summarySpan.style.transition = 'transform 0.2s ease, opacity 0.2s ease';
    summarySpan.style.transform = 'translateX(-20px)';
    summarySpan.style.opacity = '0';
    setTimeout(() => {
      summarySpan.innerHTML = newContent;
      summarySpan.style.background = newBackground;
      summarySpan.style.color = newColor;
      summarySpan.style.transform = 'translateX(0)';
      summarySpan.style.opacity = '1';
      setTimeout(() => {
        summarySpan.style.transition = '';
        isAnimating = false;
      }, 220);
    }, 200);
  };

  const resetSummaryPanel = () => {
    if (!summarySpan) return;
    animateSummaryPanel('Всего бонусов за заказ: жду твоего расчета 😊', '', '#333');
    summarySpan.dataset.hasCalculation = 'false';
  };

  const updateSummaryPanel = (total = null) => {
    if (!summarySpan) return;
    if (total === null) {
      let totalOrderBonuses = 0;
      document.querySelectorAll('.promo-tool-checkbox:checked').forEach(cb => {
        const nameBlock = cb.closest('.order-products-table-name');
        if (!nameBlock) return;
        const totalPillDiv = nameBlock.parentNode?.querySelector('.promo-pill-total');
        if (totalPillDiv) {
          const match = totalPillDiv.textContent.match(/Всего ([\d.]+) бонусов/);
          if (match) totalOrderBonuses += parseFloat(match[1]);
        }
      });
      total = totalOrderBonuses;
    }
    if (total === 0 && !summarySpan.dataset.hasCalculation) {
      resetSummaryPanel();
    } else {
      animateSummaryPanel(`Всего бонусов за заказ: <strong>${total.toFixed(2)}</strong>`, 'linear-gradient(135deg,#ff7b7b 0%,#ff3d3d 25%,#ff1e01 60%,#e50000 100%)', '#fff');
      summarySpan.dataset.hasCalculation = 'true';
    }
  };

  const hideConfirmBubble = () => {
    if (confirmBubble) {
      confirmBubble.style.transition = 'opacity 0.2s ease, transform 0.2s ease';
      confirmBubble.style.opacity = '0';
      confirmBubble.style.transform = 'translateX(-50%) translateY(20px)';
      setTimeout(() => {
        if (confirmBubble && confirmBubble.parentNode) confirmBubble.parentNode.removeChild(confirmBubble);
        confirmBubble = null;
      }, 200);
    }
  };

  const showConfirmBubble = (onConfirm) => {
    if (confirmBubble) hideConfirmBubble();
    confirmBubble = document.createElement('div');
    confirmBubble.style.cssText = "position:fixed;bottom:20px;left:50%;transform:translateX(-50%) translateY(0);background:#fff;border-radius:12px;padding:12px 20px;box-shadow:0 4px 20px rgba(0,0,0,0.15);z-index:1000000;display:flex;gap:16px;align-items:center;font-size:14px;color:#333;opacity:0;transition:opacity 0.2s ease, transform 0.2s ease;";
    const text = document.createElement('span');
    text.textContent = 'Сбросить все вычисления?';
    const yesBtn = document.createElement('button');
    yesBtn.textContent = 'Да';
    yesBtn.style.cssText = "padding:4px 12px;border:none;border-radius:8px;background:#ff1e01;color:#fff;cursor:pointer;font-size:12px;";
    const noBtn = document.createElement('button');
    noBtn.textContent = 'Нет';
    noBtn.style.cssText = "padding:4px 12px;border:none;border-radius:8px;background:#e9ecef;color:#333;cursor:pointer;font-size:12px;";
    yesBtn.onclick = () => {
      onConfirm();
      hideConfirmBubble();
    };
    noBtn.onclick = () => hideConfirmBubble();
    confirmBubble.appendChild(text);
    confirmBubble.appendChild(yesBtn);
    confirmBubble.appendChild(noBtn);
    document.body.appendChild(confirmBubble);
    setTimeout(() => {
      confirmBubble.style.opacity = '1';
      confirmBubble.style.transform = 'translateX(-50%) translateY(0)';
    }, 10);
  };

  const resetAllCalculations = () => {
    document.querySelectorAll('.promo-pills-container').forEach(container => {
      removePillsByType(container, 'common');
      removePillsByType(container, 'brand');
      removePillsByType(container, 'individual');
    });
    document.querySelectorAll('.order-products-table-name').forEach(nameBlock => {
      removeTotalPill(nameBlock);
      const individualInputDiv = nameBlock.parentNode?.querySelector('.promo-tool-individual-input');
      if (individualInputDiv) {
        const input = individualInputDiv.querySelector('input');
        if (input) input.value = '';
      }
    });
    const premiumInput = document.querySelector('.premium-panel-input');
    if (premiumInput) premiumInput.value = '';
    const basicInput = document.querySelector('.basic-panel-input');
    if (basicInput) basicInput.value = '';
    commonPercentage = 0;
    resetSummaryPanel();
    bar('Все вычисления сброшены');
  };

  const toggleHelpPanel = () => {
    if (!helpPanel || !helpContent) return;
    if (helpVisible) {
      helpContent.style.transition = 'opacity 0.2s ease';
      helpContent.style.opacity = '0';
      setTimeout(() => {
        helpPanel.style.transition = 'transform 0.25s ease, opacity 0.25s ease, margin 0.25s ease, padding 0.25s ease';
        helpPanel.style.transform = 'scaleY(0)';
        helpPanel.style.opacity = '0';
        helpPanel.style.marginTop = '0';
        helpPanel.style.paddingTop = '0';
        helpPanel.style.paddingBottom = '0';
        setTimeout(() => {
          helpPanel.style.display = 'none';
          helpContent.style.transition = '';
        }, 260);
      }, 200);
    } else {
      helpPanel.style.display = 'block';
      helpPanel.style.transform = 'scaleY(0)';
      helpPanel.style.opacity = '0';
      helpPanel.style.marginTop = '0';
      helpPanel.style.paddingTop = '0';
      helpPanel.style.paddingBottom = '0';
      helpContent.style.opacity = '0';
      setTimeout(() => {
        helpPanel.style.transition = 'transform 0.25s ease, opacity 0.25s ease, margin 0.25s ease, padding 0.25s ease';
        helpPanel.style.transform = 'scaleY(1)';
        helpPanel.style.opacity = '1';
        helpPanel.style.marginTop = '12px';
        helpPanel.style.paddingTop = '16px';
        helpPanel.style.paddingBottom = '16px';
        setTimeout(() => {
          helpContent.style.transition = 'opacity 0.2s ease';
          helpContent.style.opacity = '1';
        }, 50);
      }, 10);
    }
    helpVisible = !helpVisible;
  };

  const calculateAll = async () => {
    if (isCalculating) return;
    isCalculating = true;
    const activePercentInput = document.querySelector(activeTab === 'premium' ? '.premium-panel-input' : '.basic-panel-input');
    if (activePercentInput) {
      let p = parseFloat(activePercentInput.value);
      commonPercentage = isNaN(p) ? 0 : p;
    } else {
      commonPercentage = 0;
    }
    const checkedBoxes = [...document.querySelectorAll('.promo-tool-checkbox:checked')];
    for (const cb of checkedBoxes) {
      const nameBlock = cb.closest('.order-products-table-name');
      if (!nameBlock) continue;
      const row = nameBlock.closest('tr');
      if (!row) continue;
      const tds = row.querySelectorAll('td');
      if (tds.length < 6) continue;
      const sumCell = tds[5];
      if (!sumCell) continue;
      const sumText = sumCell.innerText.trim();
      const sumVal = getNumberFromSum(sumText);
      const { hasPromo } = detectPromo(tds);
      const container = createPillContainer(nameBlock);
      removePillsByType(container, 'common');
      removePillsByType(container, 'brand');
      removePillsByType(container, 'individual');
      removeTotalPill(nameBlock);
      let commonValue = 0, brandValue = 0, individualValue = 0;
      if (!hasPromo) {
        if (commonPercentage !== 0) {
          commonValue = sumVal * commonPercentage / 100;
        }
        if (activeTab === 'premium') {
          const title = nameBlock.textContent.trim();
          const brandName = getBrandName(title);
          if (brandName) {
            brandValue = sumVal * 20 / 100;
          }
        }
      }
      const individualInputDiv = nameBlock.parentNode?.querySelector('.promo-tool-individual-input');
      if (individualInputDiv) {
        const indInput = individualInputDiv.querySelector('input');
        if (indInput) {
          let indPercent = parseFloat(indInput.value);
          if (!isNaN(indPercent) && indPercent !== 0) {
            individualValue = sumVal * indPercent / 100;
          }
        }
      }
      const totalValue = commonValue + brandValue + individualValue;
      if (totalValue === 0) continue;
      const totalFormatted = totalValue.toFixed(2);
      let delay = 0;
      createTotalPillWithAnimation(totalFormatted, container, nameBlock, delay);
      delay += 50;
      if (!hasPromo) {
        if (commonPercentage !== 0) {
          const formatted = commonValue.toFixed(2);
          const label = activeTab === 'premium' ? 'Премиум' : 'Базовый';
          createPillWithAnimation(`${formatted} ${label}`, 'common', container, delay);
          delay += 50;
        }
        if (activeTab === 'premium') {
          const title = nameBlock.textContent.trim();
          const brandName = getBrandName(title);
          if (brandName) {
            const formatted = brandValue.toFixed(2);
            createPillWithAnimation(`${formatted} за ${brandName}`, 'brand', container, delay);
            delay += 50;
          }
        }
      }
      if (individualValue > 0) {
        const indFormatted = individualValue.toFixed(2);
        createPillWithAnimation(`${indFormatted} по категориям`, 'individual', container, delay);
        delay += 50;
      }
      await sleep(delay);
    }
    let totalOrderBonuses = 0;
    document.querySelectorAll('.promo-tool-checkbox:checked').forEach(cb => {
      const nameBlock = cb.closest('.order-products-table-name');
      if (!nameBlock) return;
      const totalPillDiv = nameBlock.parentNode?.querySelector('.promo-pill-total');
      if (totalPillDiv) {
        const match = totalPillDiv.textContent.match(/Всего ([\d.]+) бонусов/);
        if (match) totalOrderBonuses += parseFloat(match[1]);
      }
    });
    updateSummaryPanel(totalOrderBonuses);
    bar(`Кешбэк рассчитан (${commonPercentage}% ${activeTab === 'premium' ? 'Премиум' : 'Базовый'})`);
    isCalculating = false;
  };

  (async () => {
    const tab = [...document.querySelectorAll("div[class*='_tabs-item']")].find(e => e.textContent.includes("Состав заказа"));
    if (tab) {
      tab.click();
      await sleep(1200);
    }
    const rows = await waitRows();
    if (!rows) return bar("Таблица не найдена");
    const table = rows[0]?.closest("table");
    if (table) {
      table.style.borderCollapse = 'collapse';
    }
    for (const row of rows) {
      const tds = row.querySelectorAll("td");
      if (tds.length < 6) continue;
      const nameBlock = tds[0].querySelector(".order-products-table-name");
      if (!nameBlock) continue;
      if (nameBlock.querySelector(".promo-tool-checkbox")) continue;
      const name = nameBlock.textContent.trim();
      if (!name || name.toLowerCase().includes("пакет")) continue;
      const { hasPromo } = detectPromo(tds);
      const sum = (tds[5].innerText || "").trim();
      const borderColor = hasPromo ? '#69db7e' : '#ffb3b3';
      row.style.removeProperty('background');
      row.style.setProperty('border-left', `3px solid ${borderColor}`, 'important');
      row.style.setProperty('box-shadow', 'none', 'important');
      row.style.setProperty('border-top', 'none', 'important');
      row.style.setProperty('border-right', 'none', 'important');
      row.style.setProperty('border-bottom', 'none', 'important');
      nameBlock.style.display = "flex";
      nameBlock.style.alignItems = "center";
      nameBlock.style.gap = "6px";
      const cb = document.createElement("input");
      cb.type = "checkbox";
      cb.className = "promo-tool-checkbox";
      cb.checked = !hasPromo;
      cb.dataset.name = name;
      cb.dataset.sum = sum;
      cb.dataset.promo = hasPromo ? "1" : "0";
      nameBlock.prepend(cb);
      if (cb.checked) {
        createIndividualInput(nameBlock, row);
      } else {
        createPillContainer(nameBlock);
      }
    }

    document.body.addEventListener('change', e => {
      if (e.target.classList?.contains('promo-tool-checkbox')) {
        const nameBlock = e.target.closest('.order-products-table-name');
        if (!nameBlock) return;
        if (e.target.checked) {
          if (!nameBlock.parentNode?.querySelector('.promo-tool-individual-input')) createIndividualInput(nameBlock, nameBlock.closest('tr'));
          const container = createPillContainer(nameBlock);
          removePillsByType(container, 'common');
          removePillsByType(container, 'brand');
          removePillsByType(container, 'individual');
          removeTotalPill(nameBlock);
        } else {
          const individualDiv = nameBlock.parentNode?.querySelector('.promo-tool-individual-input');
          if (individualDiv) individualDiv.remove();
          const container = nameBlock.parentNode?.querySelector('.promo-pills-container');
          if (container) container.remove();
        }
        let totalOrderBonuses = 0;
        document.querySelectorAll('.promo-tool-checkbox:checked').forEach(cb => {
          const nameBlock = cb.closest('.order-products-table-name');
          if (!nameBlock) return;
          const totalPillDiv = nameBlock.parentNode?.querySelector('.promo-pill-total');
          if (totalPillDiv) {
            const match = totalPillDiv.textContent.match(/Всего ([\d.]+) бонусов/);
            if (match) totalOrderBonuses += parseFloat(match[1]);
          }
        });
        updateSummaryPanel(totalOrderBonuses);
      }
    });

    if (table && !document.getElementById("promo-tool-calc-wrap")) {
      const wrap = document.createElement("div");
      wrap.id = "promo-tool-calc-wrap";
      wrap.style.cssText = "margin:8px 0;display:flex;flex-direction:column;gap:0;";
      const controlsRow = document.createElement("div");
      controlsRow.style.cssText = "display:flex;align-items:center;gap:12px;background:#f8f9fa;padding:6px 12px;border-radius:20px;width:fit-content;flex-wrap:wrap;transition:all 0.2s ease;";
      const style = document.createElement('style');
      style.textContent = `
        .promo-tab-btn { padding:4px 12px; border:none; border-radius:18px; cursor:pointer; background:transparent; transition:all 0.2s ease; }
        .promo-tab-btn.active { background:#fff !important; font-weight:bold !important; }
        .help-btn { width:24px; height:24px; border-radius:50%; background:#e9ecef; border:none; cursor:pointer; font-size:14px; font-weight:bold; color:#666; display:inline-flex; align-items:center; justify-content:center; transition:all 0.2s ease; }
        .help-btn:hover { background:#dee2e6; color:#333; transform:scale(1.05); }
        .reset-btn { width:24px; height:24px; border-radius:50%; background:#e9ecef; border:none; cursor:pointer; font-size:14px; font-weight:bold; color:#666; display:inline-flex; align-items:center; justify-content:center; transition:all 0.2s ease; }
        .reset-btn:hover { background:#ffb3b3; color:#ff1e01; transform:scale(1.05); }
        .help-panel { background:#f8f9fa; border-radius:20px; overflow:hidden; transform-origin:top; transition:transform 0.25s ease, opacity 0.25s ease, margin 0.25s ease, padding 0.25s ease; margin-top:0; padding:0 16px; }
        .help-panel-content { font-size:13px; line-height:1.5; opacity:0; transition:opacity 0.2s ease; }
        .help-panel-content h3 { margin:0 0 12px 0; font-size:16px; font-weight:bold; }
        .help-panel-content em { font-style:italic; }
        .help-panel-content strong { font-weight:bold; }
        .help-panel-content .quote { border-left:2px solid #ddd; padding-left:12px; margin:8px 0; color:#555; }
        .help-panel-content hr { margin:12px 0; border:none; border-top:1px solid #e0e0e0; }
      `;
      document.head.appendChild(style);

      const leftGroup = document.createElement("div");
      leftGroup.style.cssText = "display:flex;align-items:center;gap:8px;";

      const helpButton = document.createElement("button");
      helpButton.textContent = "?";
      helpButton.className = "help-btn";
      helpButton.title = "Как пользоваться калькулятором?";
      helpButton.onclick = toggleHelpPanel;
      leftGroup.appendChild(helpButton);

      const tabsContainer = document.createElement("div");
      tabsContainer.style.cssText = "display:flex;gap:4px;background:#e9ecef;border-radius:20px;padding:2px;";
      const premiumTab = document.createElement("button");
      premiumTab.textContent = "Премиум";
      premiumTab.className = "promo-tab-btn";
      if (activeTab === 'premium') premiumTab.classList.add('active');
      const basicTab = document.createElement("button");
      basicTab.textContent = "Базовый";
      basicTab.className = "promo-tab-btn";
      if (activeTab === 'basic') basicTab.classList.add('active');
      premiumTab.onmouseenter = () => { if (!premiumTab.classList.contains('active')) premiumTab.style.background = '#dee2e6'; };
      premiumTab.onmouseleave = () => { if (!premiumTab.classList.contains('active')) premiumTab.style.background = 'transparent'; };
      basicTab.onmouseenter = () => { if (!basicTab.classList.contains('active')) basicTab.style.background = '#dee2e6'; };
      basicTab.onmouseleave = () => { if (!basicTab.classList.contains('active')) basicTab.style.background = 'transparent'; };
      tabsContainer.appendChild(premiumTab);
      tabsContainer.appendChild(basicTab);
      leftGroup.appendChild(tabsContainer);

      const panelsContainer = document.createElement("div");
      panelsContainer.style.cssText = "display:flex;gap:8px;align-items:center;";

      const premiumPanel = document.createElement("div");
      premiumPanel.className = "cashback-panel";
      premiumPanel.style.cssText = "display:flex;gap:6px;align-items:center;";
      const premiumInput = document.createElement("input");
      premiumInput.type = "number";
      premiumInput.placeholder = "%";
      premiumInput.step = "any";
      premiumInput.className = "premium-panel-input";
      premiumInput.style.cssText = "width:60px;padding:4px 6px;border:1px solid #ccc;border-radius:12px;text-align:center;transition:all 0.2s ease;";
      premiumInput.addEventListener('focus', () => premiumInput.style.borderColor = '#ff1e01');
      premiumInput.addEventListener('blur', () => premiumInput.style.borderColor = '#ccc');
      premiumInput.value = "";
      premiumPanel.appendChild(premiumInput);

      const basicPanel = document.createElement("div");
      basicPanel.className = "cashback-panel";
      basicPanel.style.cssText = "display:none;gap:6px;align-items:center;";
      const basicInput = document.createElement("input");
      basicInput.type = "number";
      basicInput.placeholder = "%";
      basicInput.step = "any";
      basicInput.className = "basic-panel-input";
      basicInput.style.cssText = "width:60px;padding:4px 6px;border:1px solid #ccc;border-radius:12px;text-align:center;transition:all 0.2s ease;";
      basicInput.addEventListener('focus', () => basicInput.style.borderColor = '#ff1e01');
      basicInput.addEventListener('blur', () => basicInput.style.borderColor = '#ccc');
      basicInput.value = "";
      basicPanel.appendChild(basicInput);

      panelsContainer.appendChild(premiumPanel);
      panelsContainer.appendChild(basicPanel);

      const calcBtn = document.createElement("button");
      calcBtn.textContent = "Рассчитать кешбэк";
      calcBtn.style.cssText = "padding:6px 12px;border:none;border-radius:20px;background:#111;color:#fff;cursor:pointer;font-size:12px;transition:all 0.2s ease;";
      calcBtn.onmouseenter = () => calcBtn.style.background = '#333';
      calcBtn.onmouseleave = () => calcBtn.style.background = '#111';
      calcBtn.onclick = calculateAll;

      const resetBtn = document.createElement("button");
      resetBtn.textContent = "✕";
      resetBtn.className = "reset-btn";
      resetBtn.title = "Сбросить все вычисления";
      resetBtn.onclick = () => { showConfirmBubble(resetAllCalculations); };

      const separator = document.createElement('span');
      separator.textContent = '|';
      separator.style.cssText = "color:#ccc;margin:0 4px;";

      summarySpan = document.createElement('span');
      summarySpan.style.cssText = "font-size:13px;border-radius:20px;padding:4px 12px;display:inline-block;";
      resetSummaryPanel();

      controlsRow.appendChild(leftGroup);
      controlsRow.appendChild(panelsContainer);
      controlsRow.appendChild(calcBtn);
      controlsRow.appendChild(resetBtn);
      controlsRow.appendChild(separator);
      controlsRow.appendChild(summarySpan);

      helpPanel = document.createElement('div');
      helpPanel.className = 'help-panel';
      helpPanel.style.display = 'none';
      helpContent = document.createElement('div');
      helpContent.className = 'help-panel-content';
      helpContent.innerHTML = `
        <h3>🧮 Как пользоваться калькулятором?</h3>
        <p><em>Зеленым выделены товары, к которым применился промокод, красным — без промокода.</em></p>
        <p><strong>1.</strong> Выбери вкладку Премиум / Базовый в зависимости от наличия подписки у клиента.</p>
        <p><strong>2.</strong> Проверь категории с кешбэком клиента</p>
        <p><strong>3.</strong> Выбери товары, по которым будем считать кешбэк:</p>
        <div class="quote">• товары без промокода — отмечены автоматически<br>• товары с промокодом — если на них действуют категории, распространяющиеся на товары по акции</div>
        <p><strong>4.</strong> Введи процент кешбэка Премиум / Базовый в поле рядом с вкладками:</p>
        <div class="quote">• Премиум — 5% для заказов Экспресс, Доставка и Аптека, 10% для заказов Косметик<br>• Базовый — процент в категории. Если не выбран, оставь поле пустым</div>
        <p><strong>5.</strong> У товаров, на которые действуют категории, укажи процент кешбэка за категорию</p>
        <p><strong>6.</strong> Нажми «Рассчитать кешбэк» — появятся детализация по бонусам у каждого товара и общая сумма бонусов за заказ</p>
        <p><strong>7.</strong> Если после расчета нужно что-то исправить, внеси изменения и нажми «Рассчитать кешбэк» — расчеты обновятся</p>
        <hr>
        <p><strong>Примечание:</strong></p>
        <p>По Премиуму есть дополнительный кешбэк:<br> 🥗 20% за готовую еду и выпечку собственного производства (СП)<br> 🎁 20% за товары брендов Premiere of Taste, La Fresh, Восточный гость, EatMeat, Inspirato Momento и М Кухня<br> ☕ 50% за чай и кофе</p>
        <p>✔️ За товары брендов калькулятор сам посчитает и отобразит дополнительный кешбэк<br> ✖️ За СП и чай / кофе нужно посчитать самостоятельно:</p>
        <div class="quote">• Проверь, есть ли в заказе товары СП — у таких товаров название без обозначения бренда. Например:<br> <em>– Фунчоза с овощами по-корейски 150г</em><br> <em>– Сочник с творогом 110г</em><br> <em>– Кофе Капучино большой зерновой 300мл</em><br><br> • Если такие товары есть в заказе — укажи 20% (готовая еда и выпечка) или 50% (чай и кофе) в поле для индивидуального кешбэка</div>
      `;
      helpPanel.appendChild(helpContent);

      wrap.appendChild(controlsRow);
      wrap.appendChild(helpPanel);

      const switchTab = (active) => {
        activeTab = active;
        if (active === 'premium') {
          premiumTab.classList.add('active');
          basicTab.classList.remove('active');
          premiumPanel.style.display = "flex";
          basicPanel.style.display = "none";
        } else {
          basicTab.classList.add('active');
          premiumTab.classList.remove('active');
          basicPanel.style.display = "flex";
          premiumPanel.style.display = "none";
        }
        document.querySelectorAll('.promo-pills-container').forEach(container => {
          removePillsByType(container, 'common');
          if (active === 'basic') removePillsByType(container, 'brand');
        });
        document.querySelectorAll('.order-products-table-name').forEach(nameBlock => {
          removeTotalPill(nameBlock);
        });
        resetSummaryPanel();
      };

      premiumTab.onclick = () => switchTab('premium');
      basicTab.onclick = () => switchTab('basic');

      table.parentNode.insertBefore(wrap, table);
    }
    bar("Готово");
  })();
})();
